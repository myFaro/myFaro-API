## Oauth 2.0

Request your client id and secret from info@myfaro.be.

### GET    /api/v1/fast

Endpoint for fast token validation.

##### curl command

```
curl -H "Authorization: Bearer 53cff8f4a549beb1c38704158b0f6608a2382f094b6947ecc35c2eed4146a17c" \
     localhost:3000/api/v1/fast
```

##### command output
```
{"ok": true}
```

### GET    /api/v1/me

Endpoint for fetching current user data.

##### curl command

```
curl -H "Authorization: Bearer 53cff8f4a549beb1c38704158b0f6608a2382f094b6947ecc35c2eed4146a17c" \
     localhost:3000/api/v1/me
```

##### command output
```
{
    "uid": "5e021e7c5eb6f54eef65e293",
    "name": "John Doe",
    "email": "john.doe@gmail.com",
    "role": {
        "is_broker_admin": true,
        "is_agent": false,
        "is_professional": true
    }
}
```

### POST    /api/v1/broker/link

Endpoint to link the broker user account with token (resources owner) user.

##### Required params to send

```
{ partner: {
    uid: "broker uid"
  }
}
```

##### possible HTTP response

* 201 - when resource and params sent are valid
* 404 - when broker not found
* 422 - when token is valid but params not or link already exists

##### curl command

```
curl --location --request POST 'http://localhost:3000/api/v1/broker/link' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer a8944cae979f21b04bcc11b8c80e16ed2b6f7a3f8f863999e2e4a2254c240712' \
  --data-raw '{"partner": {"uid": "1234"}}'
```

##### command output
```
{
    "uid": "5e021e7c5eb6f54eef65e293",
    "linked_application": [{
        "uid": "1234",
        "application_id": "1234"
    }]
}
```



GET Party
----
Get the details for a Party using the myFaro uid

* **URL**

  /api/v1/parties/:uid

* **Method:**

  `GET`
  
*  **URL Params**

   **Required:**
 
   `uid=[string]`

* **Success Response:**

  * **Code:** 200 <br />
    **Content:** `see Party model definition`
 
* **Error Response:**

  * **Code:** 404 PARTY NOT FOUND <br />
    **Content:** `{ errors : "PARTY NOT FOUND" }`
  
POST Party
----
Create a new Party

* **URL**

  /api/v1/parties

* **Method:**

  `POST`
  
*  **URL Params**
 
   `none`

* **Data Params**

   `see Party model definition`
   
   The :broker_crm_group_id and at least one unique identifier must be present
   * ** :national_id
   * ** :id_card
   * ** :broker_crm_id

* **Success Response:**
  
  * **Code:** 201 <br />
    **Content:** `see Party model definition`
 
* **Error Response:**

  * **Code:** 404 CASE FILE NOT FOUND <br />
    **Content:** `{ errors: "CASE FILE NOT FOUND" }`

  OR

  * **Code:** 422 SAVE FAILED <br />
    **Content:** `{ errors : save error messages }`
    
    
  OR

  * **Code:** 422 BROKER_CRM_GROUP_IP NOT PRESENT <br />
    **Content:** `{ errors : "BROKER_CRM_GROUP_IP NOT PRESENT" }`
    
    
  OR

  * **Code:** 422 NO UNIQUE PARTY IDENTIFIER PRESENT <br />
    **Content:** `{ errors : "NO UNIQUE PARTY IDENTIFIER PRESENT" }`
    
    
  OR

  * **Code:** 422 PARTY EXISTS IN OTHER CASE_FILE <br />
    **Content:** `{ errors : "PARTY EXISTS IN OTHER CASE_FILE" }`
    
    
  OR

  * **Code:** 422 NOT A PARTNER OR OWN_COMPANY <br />
    **Content:** `{ errors : "NOT A PARTNER OR OWN_COMPANY" }`
    
  OR

  * **Code:** 422 ALREADY 2 PARTNERS <br />
    **Content:** `{ errors : "ALREADY 2 PARTNERS" }`

MODEL Party
----

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :party_uid  | String | the myFaro UID which is returned to the Application after creation |
| :case_file_uid | String |  |
| :broker_crm_id | String | the user visible party ID in the application is a field which is stored by myFaro and is used as a unique ID to reference the party in the application |
| :broker_crm_group_id | String | a group id which is used to link parties in a casefile in myFaro |
| :national_id | String |  || Content | Content |  |
| :id_card | String |  |
| :party_role | String | head_of_family - partner - community - third_party - own_company - employer - child - parent - grandchild - brother_sister - ex_partner - grandparent - nephew_niece - uncle_aunt - party_role_other|
| :gender | String | male - female - legal_entity |
| :first_name | Content |  |
| :last_name | Content |  |
| :force_creation | Boolean |  |
| :case_file_type | String | Report - View - Insight - Insight+ - LifePlan |

