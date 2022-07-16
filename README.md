## Задача 1
Найдите, где перечислены все доступные resource и data_source, приложите ссылку на эти строки в коде на гитхабе.

Доступные resource и data_source перечислены в файле /internal/provider/provider.go

data_source: https://github.com/hashicorp/terraform-provider-aws/blob/main/internal/provider/provider.go#:~:text=DataSourcesMap%3A%20map%5Bstring%5D*schema.Resource%7B

resource: https://github.com/hashicorp/terraform-provider-aws/blob/main/internal/provider/provider.go#:~:text=ResourcesMap%3A%20map%5Bstring%5D*schema.Resource%7B

Для создания очереди сообщений SQS используется ресурс aws_sqs_queue у которого есть параметр name.
 - С каким другим параметром конфликтует name? Приложите строчку кода, в которой это указано.
 
 Конфликтует с параметром name_prefix  https://github.com/hashicorp/terraform-provider-aws/blob/e7992035fe51e39ed877c2d7563c4a5549f55722/internal/service/sqs/queue.go#L169:~:text=ConflictsWith%3A%20%5B%5Dstring%7B%22name_prefix%22%7D%2C
```
		"name": {
			Type:          schema.TypeString,
			Optional:      true,
			Computed:      true,
			ForceNew:      true,
			ConflictsWith: []string{"name_prefix"},
		},
```
 - Какая максимальная длина имени?
 
 В коде не удалось найти проверку на максимальную длину имени
 - Какому регулярному выражению должно подчиняться имя?
 
 Если задан префикс - имя генерируется на его основе по следующему алгоритму
 
 {name_prefix}{timestamp}{Hexadecimal counter}{namesuffix}
 
 timestamp - берётся до 10000 доли секунды, точка удаляется. в итоге получается строка из цифр длинной 18 символов
 
 Hexadecimal counter - простой счетчик идентификаторов, увеличивается при каждом создании имени. Дописывается нолями до 8 символов.
 
 В итоге длина части имени {timestamp}{Hexadecimal counter} составляет 26 символов. 
 
 Если префикс имени не задан - вместо него вписывается "terraform-"
