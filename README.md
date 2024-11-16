## Домашнее задание к занятию «Как работает сеть в K8s»
#### Цель задания
- Настроить сетевую политику доступа к подам.

#### Чеклист готовности к домашнему заданию
- Кластер K8s с установленным сетевым плагином Calico.
#### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания
- Документация Calico.
- Network Policy.
- About Network Policy.

### Задание 1. Создать сетевую политику или несколько политик для обеспечения доступа
1. Создать deployment'ы приложений frontend, backend и cache и соответсвующие сервисы.
2. В качестве образа использовать network-multitool.
3. Разместить поды в namespace App.
4. Создать политики, чтобы обеспечить доступ frontend -> backend -> cache. Другие виды подключений должны быть запрещены.
5. Продемонстрировать, что трафик разрешён и запрещён.

### Решение задания 1. Создать сетевую политику или несколько политик для обеспечения доступа

Т.к. в MicroK8S установлен плагин Calico из коробки, то буду успользовать его для выполнения задания

![img1_1](https://github.com/user-attachments/assets/47ed8cc3-0eaa-4c53-8baf-e0df891d13a8)

Пишу манифесты deployment'ов приложений frontend, backend и cache и соответсвующие им сервисы.

В манифестах deployment'ов в качестве образа используется network-multitool.

Для размещения подов в namespace app создам этот namespace:

![img1_2](https://github.com/user-attachments/assets/d48eed0f-3a92-484a-9fa2-51b9830c3d6c)

Также в метаданных манифестов deployment'ов и сетевых политик сразу укажу использование namespace app.

Пишу манифесты сетевых политик, которые будут разрешать обращаться к приложению пода backend из frontend а также будут разрешать обращаться к приложению пода cache из пода backend. Все остальное будет запрещено. Сначала запрещается весь сетевой трафик, потом разрешается то, что должно быть разрешено.
Ссылка на запрещающий манифест - https://github.com/slava1005/3_3/blob/main/manifest/deny-all.yaml

Ссылка на манифест разрешающий обращаться к backend из frontend - https://github.com/slava1005/3_3/blob/main/manifest/front-to-back.yaml

Ссылка на манифест разрешающий обращаться к cache из backend - https://github.com/slava1005/3_3/blob/main/manifest/back-to-cache.yaml

Применю манифесты deployment'ов и сетевых политик:

![img1_3](https://github.com/user-attachments/assets/2bbbd5fc-9568-4cbd-a9f2-1fe98c1e44f2)

Проверяю результат:

![img1_4](https://github.com/user-attachments/assets/5597233e-e693-4120-8a9c-5d390a7028fe)

![img1_5](https://github.com/user-attachments/assets/285380b7-646c-4fdc-ab90-590eff88b2dd)

![img1_6](https://github.com/user-attachments/assets/c2fa532e-0ba5-44ca-973a-c66da8ebc6ab)

Создались deployment'ы, поды и сетевые политики в соответствии с заданием.

Проверю, что трафик из frontend в backend и из backend в cache разрешен, остальное запрещено.

В соответствии с моими deployment'ами приложения в подах слушают порт 1180, соответственно к нему я и буду подключаться.

Захожу на под с приложением frontend и проверю, можно ли из него обратиться к приложению backend:

![img1_7](https://github.com/user-attachments/assets/25e39a92-b547-41fc-8130-9ca228b4a6c8)

Видно, что frontend имеет доступ к самому себе и имеет доступ к приложению backend, но не имеет доступ к приложению cache.

Захожу на под с приложением backend и проверю, можно ли из него обратиться к приложению cache:

![img1_8](https://github.com/user-attachments/assets/806f18d2-fd91-4e41-849e-49429d7d03a7)

Видно, что backend имеет доступ к самому себе и имеет доступ к приложению cache, но не имеет доступ к приложению frontend.

Захожу на под с приложением cache и проверю, можно ли из него обратиться к приложениям frontend и backend:

![img1_9](https://github.com/user-attachments/assets/e11341ac-7c20-4ae0-ae97-e7d98e85871e)

Видно, что cache имеет доступ к самому себе, но не имеет доступ к приложениям frontend и backend.
