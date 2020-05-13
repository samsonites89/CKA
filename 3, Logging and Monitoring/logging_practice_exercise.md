
A user - 'USER5' - has expressed concerns accessing the application. Identify the cause of the issue.
Inspect the logs of the POD

```shell script
master $ kubectl logs -f --tail 10 webapp-1
[2020-05-10 15:11:45,615] INFO in event-simulator: USER1 is viewing page1
[2020-05-10 15:11:46,617] INFO in event-simulator: USER2 logged in
[2020-05-10 15:11:47,618] INFO in event-simulator: USER3 is viewing page2
[2020-05-10 15:11:48,621] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-05-10 15:11:48,621] INFO in event-simulator: USER2 logged out
[2020-05-10 15:11:49,622] INFO in event-simulator: USER3 is viewing page1
[2020-05-10 15:11:50,624] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2020-05-10 15:11:50,624] INFO in event-simulator: USER3 is viewing page1
[2020-05-10 15:11:51,626] INFO in event-simulator: USER4 is viewing page3
[2020-05-10 15:11:52,628] INFO in event-simulator: USER1 logged out
[2020-05-10 15:11:53,639] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-05-10 15:11:53,639] INFO in event-simulator: USER4 is viewing page2
[2020-05-10 15:11:54,641] INFO in event-simulator: USER2 is viewing page3
[2020-05-10 15:11:55,702] INFO in event-simulator: USER3 is viewing page2
[2020-05-10 15:11:56,704] INFO in event-simulator: USER1 logged out
[2020-05-10 15:11:57,706] INFO in event-simulator: USER2 is viewing page1

## Failed attempts for USEER5
[2020-05-10 15:11:58,709] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-05-10 15:11:58,709] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2020-05-10 15:11:58,709] INFO in event-simulator: USER4 is viewing page1
[2020-05-10 15:11:59,733] INFO in event-simulator: USER4 is viewing page3
```



A user is reporting issues while trying to purchase an item. Identify the user and the cause of the issue.
Inspect the logs of the webapp in the POD


```shell script
master $ kubectl logs -f webapp-2 --tail 10
Error from server (BadRequest): a container name must be specified for pod webapp-2, choose one of: [simple-webapp db]

# 해당 pod 은 다수의 container가 존재함으로 container 이름 지정 필요
master $ kubectl logs -f webapp-2 --tail 10 simple-webapp
[2020-05-10 15:15:10,693] INFO in event-simulator: USER2 is viewing page3
[2020-05-10 15:15:11,694] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-05-10 15:15:11,694] INFO in event-simulator: USER2 is viewing page1
[2020-05-10 15:15:12,696] INFO in event-simulator: USER1 is viewing page3
[2020-05-10 15:15:13,696] INFO in event-simulator: USER4 is viewing page1
[2020-05-10 15:15:14,698] INFO in event-simulator: USER2 is viewing page2
[2020-05-10 15:15:15,699] WARNING in event-simulator: USER30 Order failed as the item is OUT OF STOCK.
[2020-05-10 15:15:15,700] INFO in event-simulator: USER3 is viewing page3
[2020-05-10 15:15:16,700] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-05-10 15:15:16,701] INFO in event-simulator: USER2 is viewing page3
[2020-05-10 15:15:17,702] INFO in event-simulator: USER4 logged in
[2020-05-10 15:15:18,703] INFO in event-simulator: USER3 logged in
[2020-05-10 15:15:19,705] INFO in event-simulator: USER2 logged out
[2020-05-10 15:15:20,707] INFO in event-simulator: USER2 logged out
[2020-05-10 15:15:21,708] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-05-10 15:15:21,709] INFO in event-simulator: USER2 is viewing page2

```
