[task](https://github.com/rolling-scopes-school/tasks/blob/master/tasks/presentation.md)

- [YouTube video](youtube.com)  
- [presentation's deploy]()  
- [transcript presentation]()  

---  

## JWT
---  
---   

#### 0. Introduce
Hello everyone! My name is AV 
Today I would like to introduce a presentation on JWT.

#### 0. Content
In this presentation we'll explore the following questions:
1. The Problem
2. What is a JWT 
3. Structure
4. JWT Claims
5. JWT Authentication With Refresh Tokens
...

#### 1. The Problem  
Long time ago we have next standart realize project

client  (request)----------> 		server (generate random id and save information about user, then responce )
		<---------(responce) id		
			(cookei)

```			
Клиент отправляет запрос на сервер (Auth auth.site.com). Сервер случайным образом генерирует id  for user and save this information into storage.
client every own response подписывал эти id, сервер смотрел в своем хранилище и сверял этот id.
Все работало до появления микросервисов и внешней авторизации.

Проблема.
У нас есть сервер Авторизации Auth auth.site.com и сервер данных API api.data.com. Это разные сервера.
Если клиент будет отправлять id в cookie, то они автоматически выставятся для  auth.site.com, что нам не подходит, при обращении к api.data.com. Если решить эту проблему и выставлять id в виде plain-text (for example in body response), то возникнет проблема, что отсылая id к api.data.com, самому серверу(api.data.com) надо сделать запрос на сервер Auth (), чтобы узнать валидность id и данные самого пользователя.  
Для решения этой проблемы можно использовать криптографию. Для этого Серер должен выдавать не просто id, а полноценный id, который в дополнение будет подписан секретным ключом. Секретный ключ - это строка, которая выдана в настройках и сервера-данных. И является идентичной на обоих серверах. Подпись - это результат хэширования данных пользователя. Клиент может виеть данные, которые он получил, однако клиент не может их изменить. ПОтому, что тогда подпись будет неверной.  Когда, пользователь отправляет данные на сервер-данных API, этот сервер сверяет подпись пользователя с той что он рассчитал на основе секретного ключа.
И этим подтверждается корректность (неизменность) данных. 
???? Для того, чтобы убрать данную дополнительную связь (between auth and api), а также улучшить защищенность данных стали использовать JWT-токен.  
```

The client sends a request to the server (Auth auth.site.com). The server randomly generates users id and save this information into storage.
Client every own response signed these id, server looking for id in storage and check it.

Everything worked fine until the emergence of microservices and external authorization.

The problem is.
We have an Auth Auth.site.com authorization server and an api.data.com API data server. These are different servers.

If the client sends the id in a cookie, it will automatically be set for auth.site.com, which does not work for access to api.data.com. If we solve this problem and expose id as plain-text (for example in body response), then there is a problem that sending id to api.data.com, the server itself (api.data.com) must make a request to the server Auth (), to know the validity of id and full data of the user.  

To solve this problem, we can use cryptography. To do this, Serer must issue not just a simple id, but a full id, which in addition will be signed with a secret key. The secret key is a string that is issued in the settings and server-data. And is identical on both servers. The signature is the result of hashing the user's data. The client can see the data it has received but the client cannot change it. This is because then the signature will not be correct.  When the user sends data to the API data server, that server checks the user's signature against what he calculated based on the secret key. And it confirms the correctness (immutability) of the data.


#### 2. What is a JWT  
A JSON Web Token (JWT) is a JSON object that is defined in RFC 7519 as a safe way of transmitting information between two parties. Information in the JWT is digitally-signed, so that it can be verified and trusted.		

#### 3. Structure
A JWT is just a string, which contains three parts: headers, payload and signature.  
* Header: contains information about how the JWT signature should be calculated. This is a JSON object that looks like this:
{
  "alg": "HS256",
  "typ": "JWT"
}

and consists of two parts:
	- The signing algorithm that’s being used.	
	- The type of token, which, in this case, is mostly “JWT”.


* Payload: The payload contains the claims or the JSON object.
```это полезные данные, которые хранятся внутри JWT. Эти данные также называют JWT-claims (заявки).
Эти поля могут быть полезными при создании JWT, но они не являются обязательными. Cтоит помнить, что чем больше передается информации, тем больший получится в итоге сам JWT. Обычно с этим не бывает проблем, но все-таки это может негативно сказаться на производительности и вызвать задержки во взаимодействии с сервером.
```
Payload is useful data that is stored inside the JWT. This data is also called JWT-claims.  
These fields may be useful when creating a JWT, but they are not mandatory. It is worth remembering that the more information that is transferred, the larger the JWT itself will end up. Usually there is no problem with this, but it can still have a negative impact on performance and cause delays in communication with the server.
example:
{
  "sub": "0123456789",
  "name": "John Bonn",
  "iat": 1662034022
}

* Signature: A string that is generated via a cryptographic algorithm that can be used to verify the integrity of the JSON payload.
https://supertokens.com/static/b0172cabbcd583dd4ed222bdb83fc51a/40601/jwt-structure.png

Example  
https://jwt.io/
{
  "alg": "HS256",
  "typ": "JWT"
}
{
  "sub": "0123456789",
  "name": "John Bonn",
  "iat": 1662034022
}
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),  
	secret
) secret base64 encoded

#### 4. JWT Claims (заявки)
https://auth0.com/docs/secure/tokens/json-web-tokens/json-web-token-claims
There are two types of JWT claims: Registered and Custom.

Registered: standard claims registered with the Internet Assigned Numbers Authority (IANA) and defined by the JWT specification to ensure interoperability with third-party, or external, applications. OIDC standard claims are reserved claims.
Custom:  consists of non-registered public or private claims. Public claims are collision-resistant while private claims are subject to possible collisions.

Registered claims
The JWT specification defines seven reserved claims that are not required, but are recommended to allow interoperability with third-party applications. These are: (you can see then on screen)
-iss (issuer): Issuer of the JWT
-sub (subject): Subject of the JWT (the user)
-aud (audience): Recipient for which the JWT is intended
-exp (expiration time): Time after which the JWT expires
-nbf (not before time): Time before which the JWT must not be accepted for processing
-iat (issued at time): Time at which the JWT was issued; can be used to determine age of the JWT
-jti (JWT ID): Unique identifier; can be used to prevent the JWT from being replayed (allows a token to be used only once)
You can see a full list of registered claims at the IANA JSON Web Token Claims Registry.

Custom claims
You can define your own custom claims which you control and you can add them to a token using Actions. Here are some examples:
- Add a user's email address to an access token and use that to uniquely identify the user.
- Add custom information stored in an Auth0 user profile to an ID token.
As long as the Action is in place, the custom claims it adds will appear in new tokens issued when using a refresh token.
For an example showing how to add custom claims to a token, see Sample Use Cases: Scopes and Claims.



#### 5. How do JSON Web Tokens work?  

#### 6. JWT Authentication With Refresh Tokens
https://www.geeksforgeeks.org/jwt-authentication-with-refresh-tokens/?ref=rp
When building a web application, authentication is one of the important aspects, and we usually implement authentication using JWT tokens (You can learn more about JWT here). We create an access token and store it in the local storage or session or cookie. But there is a more secure way to implement this using Refresh Tokens.

Refresh Tokens: It is a unique token that is used to obtain additional access tokens. This allows you to have short-lived access tokens without having to collect credentials every time one expires.

Since access tokens aren’t valid for an extended period because of security reasons, a refresh token helps to re-authenticate a user without login credentials. This Refresh token is never exposed to the client-side Javascript, even if our access token gets compromised it’ll be expired in a very short duration. So, we will be sending two tokens instead of one, an access token and a refresh token. The access token will contain all the user information and will be stored in Javascript runtime, but the refresh token will be stored securely in an HTTP-only cookie. 
pic. jwt-auth-w-refresh-token-01

Auth Persistence: We can easily persist users between refreshes and login without any credentials. We can create a new route called refresh, whenever a token expires or a user refreshes we can get a new access token by sending a request to this route
pic. jwt-auth-w-refresh-token-02













2. Basic
JWTs can be used in various ways:

Authentication: When a user successfully logs in using their credentials, an ID token is returned. According to the OpenID Connect (OIDC) specs, an ID token is always a JWT.

Authorization: Once a user is successfully logged in, an application may request to access routes, services, or resources (e.g., APIs) on behalf of that user. To do so, in every request, it must pass an Access Token, which may be in the form of a JWT. Single Sign-on (SSO) widely uses JWT because of the small overhead of the format, and its ability to easily be used across different domains.

Information Exchange: JWTs are a good way of securely transmitting information between parties because they can be signed, which means you can be sure that the senders are who they say they are. Additionally, the structure of a JWT allows you to verify that the content hasn't been tampered with.

Для начала рассмотрим понятия Аутентификация и Авторизация

Аутентификация(authentication) - это процесс проверки учётных данных пользователя (логин/пароль). Проверка подлинности пользователя путём сравнения введённого им логина/пароля с данными сохранёнными в базе данных.
Авторизация(authorization) - это проверка прав пользователя на доступ к определенным ресурсам.

#### 3. What is a JWT  
JSON Web Token is a proposed standard (RFC 7519) for creating data with optional signature and/or optional encryption whose payload holds JSON that asserts some number of claims. The tokens are signed either using a private secret or a public/private key.




2. Structure
A JWT contains three parts:

* Header: содержит информацию о том, как должна вычисляться JWT подпись. This is JSON объект, который выглядит следующим образом:
Consists of two parts:
	- The signing algorithm that’s being used.	
	- The type of token, which, in this case, is mostly “JWT”.
{
  "alg": "HS256",
  "typ": "JWT"
}
* Payload: The payload contains the claims or the JSON object.
это полезные данные, которые хранятся внутри JWT. Эти данные также называют JWT-claims (заявки).
Эти поля могут быть полезными при создании JWT, но они не являются обязательными. Если хотите знать весь список доступных полей для JWT, можете заглянуть в Wiki. Но стоит помнить, что чем больше передается информации, тем больший получится в итоге сам JWT. Обычно с этим не бывает проблем, но все-таки это может негативно сказаться на производительности и вызвать задержки во взаимодействии с сервером.
{
  "sub": "0123456789",
  "name": "John Bonn",
  "iat": 1662034022
}
* Signature: A string that is generated via a cryptographic algorithm that can be used to verify the integrity of the JSON payload.
https://supertokens.com/static/b0172cabbcd583dd4ed222bdb83fc51a/40601/jwt-structure.png

Example  
https://jwt.io/
{
  "alg": "HS256",
  "typ": "JWT"
}
{
  "sub": "0123456789",
  "name": "John Bonn",
  "iat": 1662034022
}
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),  
	secret
) secret base64 encoded

Payload.


10. JWT Authentication With Refresh Tokens
https://www.geeksforgeeks.org/jwt-authentication-with-refresh-tokens/?ref=rp
When building a web application, authentication is one of the important aspects, and we usually implement authentication using JWT tokens (You can learn more about JWT here). We create an access token and store it in the local storage or session or cookie. But there is a more secure way to implement this using Refresh Tokens.

Refresh Tokens: It is a unique token that is used to obtain additional access tokens. This allows you to have short-lived access tokens without having to collect credentials every time one expires.

Since access tokens aren’t valid for an extended period because of security reasons, a refresh token helps to re-authenticate a user without login credentials. This Refresh token is never exposed to the client-side Javascript, even if our access token gets compromised it’ll be expired in a very short duration. So, we will be sending two tokens instead of one, an access token and a refresh token. The access token will contain all the user information and will be stored in Javascript runtime, but the refresh token will be stored securely in an HTTP-only cookie. 
pic. jwt-auth-w-refresh-token-01

Auth Persistence: We can easily persist users between refreshes and login without any credentials. We can create a new route called refresh, whenever a token expires or a user refreshes we can get a new access token by sending a request to this route
pic. jwt-auth-w-refresh-token-02


10. JWT Claims (заявки)
https://auth0.com/docs/secure/tokens/json-web-tokens/json-web-token-claims
There are two types of JWT claims:

Registered: standard claims registered with the Internet Assigned Numbers Authority (IANA) and defined by the JWT specification to ensure interoperability with third-party, or external, applications. OIDC standard claims are reserved claims.

Custom:  consists of non-registered public or private claims. Public claims are collision-resistant while private claims are subject to possible collisions.

Registered claims
The JWT specification defines seven reserved claims that are not required, but are recommended to allow interoperability with third-party applications. These are:

-iss (issuer): Issuer of the JWT
-sub (subject): Subject of the JWT (the user)
-aud (audience): Recipient for which the JWT is intended
-exp (expiration time): Time after which the JWT expires
-nbf (not before time): Time before which the JWT must not be accepted for processing
-iat (issued at time): Time at which the JWT was issued; can be used to determine age of the JWT
-jti (JWT ID): Unique identifier; can be used to prevent the JWT from being replayed (allows a token to be used only once)
You can see a full list of registered claims at the IANA JSON Web Token Claims Registry.

Custom claims
You can define your own custom claims which you control and you can add them to a token using Actions. Here are some examples:
Add a user's email address to an access token and use that to uniquely identify the user.
Add custom information stored in an Auth0 user profile to an ID token.
As long as the Action is in place, the custom claims it adds will appear in new tokens issued when using a refresh token.
For an example showing how to add custom claims to a token, see Sample Use Cases: Scopes and Claims.

Public claims
You can create custom claims for public consumption, which might contain generic information like name and email. If you create public claims, you must either register them or use collision-resistant names through namespacing and take reasonable precautions to make sure you are in control of the namespace you use.
In the IANA JSON Web Token Claims Registry, you can see some examples of public claims registered by OpenID Connect (OIDC):
auth_time
acr
nonce

Private claims
You can create private custom claims to share information specific to your application. For example, while a public claim might contain generic information like name and email, private claims would be more specific, such as employee ID and department name.


3. JWT Authentication With Refresh Tokens




Для начала рассмотрим понятия Аутентификация и Авторизация

Аутентификация(authentication) - это процесс проверки учётных данных пользователя (логин/пароль). Проверка подлинности пользователя путём сравнения введённого им логина/пароля с данными сохранёнными в базе данных.
Авторизация(authorization) - это проверка прав пользователя на доступ к определенным ресурсам.

Например, после аутентификации юзер sasha получает право обращаться и получать от ресурса "super.com/vip" некие данные. Во время обращения юзера sasha к ресурсу vip система авторизации проверит имеет ли право юзер обращаться к этому ресурсу (проще говоря переходить по неким разрешенным ссылкам)

1-Юзер c емайлом sasha_gmail.com успешно прошел аутентификацию
2-Сервер посмотрел в БД какая роль у юзера
3-Сервер сгенерил юзеру токен с указанной ролью
4-Юзер заходит на некий ресурс используя полученный токен
5-Сервер смотрит на права(роль) юзера в токене и соответственно пропускает или отсекает запрос
Собственно п.5 и есть процесс авторизации.

Как правило, при использовании JSON-токенов в клиент-серверных приложениях реализована следующая схема:
1-Клиент проходит аутентификацию в приложении (к примеру, с использованием логина и пароля)
2-В случае успешной аутентификации сервер отправляет клиенту access- и refresh-токены.
3-При дальнейшем обращении к серверу клиент использует access-токен. Сервер проверяет токен на валидность и предоставляет клиенту доступ к ресурсам
4-В случае, если access-токен становится невалидным, клиент отправляет refresh-токен, в ответ на который сервер предоставляет два обновленных токена.
5-В случае, если refresh-токен становится невалидным, клиент опять должен пройти процесс аутентификации (п. 1).[4]


Дабы не путаться с понятиями Authentication/Authorization можно использовать псевдонимы checkPassword/checkAccess()
AccessToken - имеет малое время жизни, и является многоразовым, его добавляют к каждому последующему запросу
RefreshToken - имеет большое время жизни, и является одноразовым, его используют для обновление пары токенов

или (2-й вариант)
Клиент вводит логин и пароль.
Сервер проверяет их, и если они верны, высылает в ответе JWT-токен.
Далее клиент высылает JWT-токен при каждом последующем запросе к серверу в заголовке Authorization
Сервер проверяет JWT-токен на подлинность.
И высылает ответ.

JWT-токен 
-Выдается он клиенту после успешного ввода имени и пароля.
-С последующими запросами клиент отправляет его серверу в заголовке, сервер проверяет его подлинность путем простой калькуляции. Удостоверяется что клиент есть  тот, за кого себя выдает.
-JWT-токен продолжает работать и после того, как сервер перезапущен.
-JWT-токен работает, если серверов несколько (с балансировщиком нагрузки). На конкретный сервер ничего не завязано: сессий нет, данных, хранящихся в сессии нет.

JSON Web Token (JWT) — содержит три блока, разделенных точками: заголовок(header), набор полей (payload) и сигнатуру. Первые два блока представлены в JSON-формате и дополнительно закодированы в формат base64. Набор полей содержит произвольные пары имя/значения, притом стандарт JWT определяет несколько зарезервированных имен (iss, aud, exp и другие). Сигнатура может генерироваться при помощи и симметричных алгоритмов шифрования, и асимметричных. Кроме того, существует отдельный стандарт, отписывающий формат зашифрованного JWT-токена.

Токены предоставляют собой средство авторизации для каждого запроса от клиента к серверу. Токены(и соответственно сигнатура токена) генерируются на сервере основываясь на секретном ключе(который хранится на сервере) и payload'e. Токен в итоге хранится на клиенте и используется при необходимости авторизации какого-либо запроса. Такое решение отлично подходит при разработке SPA.

При попытке хакером подменить данные в header'ре или payload'е, токен станет не валидным, поскольку сигнатура не будет соответствовать изначальным значениям. А возможность сгенерировать новую сигнатуру у хакера отсутствует, поскольку секретный ключ для зашифровки лежит на сервере.

access token - используется для авторизации запросов и хранения дополнительной информации о пользователе (аля user_id, user_role или еще что либо, эту информацию также называет payload). Все поля в payload это свободный набор полей необходимый для реализации вашей частной бизнес логики. То бишь user_id и user_role не являются требованием и представляют собой исключительно частный случай. Сам токен храним не в localStorage как это обычно делают, а в памяти клиентского приложения.

refresh token - выдается сервером по результам успешной аутентификации и используется для получения новой пары access/refresh токенов. Храним исключительно в httpOnly куке.
AccessToken - имеет малое время жизни, и является многоразовым, его добавляют к каждому последующему запросу
RefreshToken - имеет большое время жизни, и является одноразовым, его используют для обновление пары токенов

Роль рефреш токенов и зачем их хранить в БД. Рефреш на сервере хранится для учета доступа и инвалидации краденых токенов. Таким образом сервер наверняка знает о клиентах которым стоит доверять(кому позволено авторизоваться). Если не хранить рефреш токен в БД то велика вероятность того что токены будут бесконтрольно гулять по рукам злоумышленников. Для отслеживания которых нам придется заводить черный список и периодически чистить его от просроченных. В место этого мы храним лимитированный список белых токенов для каждого юзера отдельно и в случае кражи у нас уже есть механизм противодействия(описано ниже).


!! Структура JWT


!! Как проверяется подлинность токена
Берется заголовок, полезная нагрузка и секретный ключ, из них вычисляется некоторое значение — подпись.

Секретный ключ хранится на стороне сервера. С помощью него подпись вычисляется при первоначальной выдаче токена, и с помощью него же перевычисляется каждый раз, когда приходит токен (иначе говоря, токен проверяется на валидность). Если значение вычисленной подписи совпадает с тем, что в токене, то токен считается валидным.  Это принцип поверки, аналогичный тому, что используется с Simple Hash-Based Remember-Me токеном. Только как видите, для JWT не надо даже обращаться к базе и находить пароль пользователя, чтобы вычислить подпись. Пароль в калькуляцию не входит, формула такая:

HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  секретный_ключ
)
Важно понять, что JWT-токен на сервере не хранится, а просто каждый раз проверяется на подлинность с помощью вышеприведенной формулы.

Хранится только секретный ключ — он един для всех JWT-токенов, выпускаемых приложением.

!! В каком заголовке передается JWT-токен
Принято передавать его так:

Authorization: Bearer <token>