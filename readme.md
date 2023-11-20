## Healthcarology Rest API Documentation

#### AUTH: Register to healthcarology

<details>
 <summary><code>POST</code> <code><b>https://healthcarology.org/fr/api/register</b></code></summary>

##### Parameters

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | email  |  required | email   | N/A  |
> | password  |  required | string   | N/A  |
> | lasrname  |  required | string   | N/A  |
> | firstname |  required | string   | N/A  |
> | middlename|           | ?string  | N/A  |
> | phoneNumber |   | ?string   | N/A  |
> | sex      |  required | string   | male or female  |
> | birthday      |   | ?date   | N/A  |
> | nationality      |   | ?string   | N/A  |
> | province      |   | ?string   | N/A  |
> | town_id      |   | ?integer   | N/A  |
> | city_id      |   | ?integer   | N/A  |
> | avenue      |   | ?string   | N/A  |    
> | quartier      |   | ?string   | N/A  |
> | company_id      | required  | integer   | N/A  |


##### Headers

> | Name                   | content-type       |   
> -------------------------|--------------------|
> | `Content-Type`         | `application/json` |
> | `App-Key`              | `string`           |
> | `App-Secret`           | `string`           |

##### Responses

> | http code     | content-type                      | response                                                            |
> |---------------|-----------------------------------|---------------------------------------------------------------------|
> | `201`         | `application/json`        | <code>{"success": true, "message": "Account created successfully, "user": {}, "token": "string"}</code>                                |
> | `400`         | `application/json`                | `{"success":false, "message":"Bad Request"}`  
> | `401`         | `Exception`                | `Unauthorized`    

##### JSON data Example 

> ```javascript
>{
> "lastname": "Name",
> "email": "exemple@email.com",
> "password": "password",
> "company_id": 324,
> "birthday": "1997-07-20"
>}	
> ```
</details>

## Login
- **URL:** POST https://v2.healthcarology.org/api/login

```json
{
  "username": "496718665",
  "password": "password"
}
```

- **ROLE:**  IS_AUTHENTICATED_ANONYMOUSLY (not connected) 
- **Response body**: Success, code 200  
```json
{
	"token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpYXQiOjE3MDA0NzMwNTAsImV4cCI6MTcwMDQ3NjY1MCwicm9sZXMiOlsiUk9MRV9ST09UIiwiUk9MRV9VU0VSIl0sInVzZXJuYW1lIjoiNjNlMGU1MWViZDhmOCJ9.SbL_VkbXNVswL6vS1waFH-m6EojPsJJza_EgpuDUJdDb1AiPojYTFybTCWRRUSU346NYt49e92ieybYrpqRhrSepOrzboHOTy229W0DM64REcEF7G1gBmaPRKy09OloXgkX2U5-vjmELfOHL6jRNP87MdvOT6DHsOnxr0T5i9gbNqk21P5U_9WVLs_oLGE80D2_2lYjGVg5GkyIYmeaksfgOMsu5znEEYaJBunytfDc8JBOB8pNLf4B9wYSJhO-wG4tPy2ra87H4xfOuIXo8vb8FKtLQ_x9LXnbL_9ajOOIiAg6lsbM-5F1ttsUvL5dAIPSZgRXEMBVDqNw2-1gQEg",
	"data": {
		"id": 1,
		"slug": "293d800a-fa40-420e-8e10-c382382684fd"
	}
}
```
- **Unauthorize Response** : Unauthorize, code 401
```json
{
  "code": 401,
  "message": "Invalid credentials."
}
```

<!-- 
## Request Reset Password
- **URL:** POST https://v2.healthcarology.org/auth/reset/password

```json
{
	"phoneNumber":"4927897738"
}
```

- **Response body**: Success, code 200  
```json
{
	"message": "Un code de confirmation a été envoyé à ce numero",
	"data": {
		"id": 13,
		"email": null,
		"name": "Papa Jorda ",
		"lastname": null,
		"firstname": null,
		"phoneNumber": 4927897738,
		"sexe": null,
		"checker": {
			"phoneVerifiedAt": null,
			"phoneVerified": null,
			"phoneResetCode": 62983,
			"phoneCode": 67133
		},
		"uid": "c129626a-cb67-4b05-a4fa-7bd2fd23b250"
	}
}
```

- **Not found Response**
```json
{
	"hydra:title": "ConstraintViolationList",
	"hydra:description": "name: Aucun compte est associé à ce numéro téléphone",
	"violations": [
		{
			"phoneNumber": "4927s897738",
			"message": "Aucun compte est associé à ce numéro téléphone"
		}
	]
}
``` -->

## Get Tokens
- **URL:** POST https://v2.healthcarology.org/api/tokens
- **Headers:**  
    - **Content-type** : application/json
    - **Authorization** : Bearer {token}  
    - **Accept**: application/json  

```json
{
	"user": "api/users/1",
	"token":171457
}
```

- **Response body**: Success, code 200  
```json
{
	"@context": "\/api\/contexts\/Token",
	"@id": "\/api\/tokens\/4",
	"@type": "Token",
	"token": 171457,
	"user": {
		"@id": "\/api\/users\/1",
		"@type": "User",
		"id": 1,
		"email": "mukanisa@healthcarology.com",
		"name": "KASSE",
		"lastname": "Mukanisa",
		"firstname": "Christian",
		"fullName": "KASSE Mukanisa Christian",
		"sexe": "male",
		"phoneNumber": "0974794703",
		"createdAt": "2023-02-06T00:00:00+00:00"
	},
	"createdAt": "2023-11-20T09:47:52+00:00"
}
```

- **Load all tokens**
- **URL:** POST https://v2.healthcarology.org/api/tokens/all

```json
{

}
```

