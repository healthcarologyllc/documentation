## Healthcarology Rest API Documentation

#### AUTH: Register to healthcarology

 <summary><code>POST</code> <code><b>https://healthcarology/fr/api/register</b></code></summary>

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


##### Responses

> | http code     | content-type                      | response                                                            |
> |---------------|-----------------------------------|---------------------------------------------------------------------|
> | `201`         | `application/json`        | <code>{"success": true, "message": "Account created successfully, "user": {}, "token": "string"}</code>                                |
> | `400`         | `application/json`                | `{"success":false, "message":"Bad Request"}`                

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
