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

* **Success Response:**
  
  * **Code:** 201 <br />
    **Content:** `see Party model definition`
 
* **Error Response:**

  * **Code:** 404 CASE FILE NOT FOUND <br />
    **Content:** `{ errors: "CASE FILE NOT FOUND" }`

  OR
  
  * **Code:** 404 CASE FILE NOT SAVED <br />
    **Content:** `{ errors: "CASE FILE NOT SAVED" }`

  OR

  * **Code:** 422 NO UNIQUE CASEFILE IDENTIFIER PRESENT <br />
    **Content:** `{ errors : "NO UNIQUE CASEFILE IDENTIFIER PRESENT" }` <br />
    **Reason:** to identify the case_file in which to create the party, either the :case_file_uid or the :broker_crm_group_id must be present
    
  OR

  * **Code:** 422 NO UNIQUE PARTY IDENTIFIER PRESENT <br />
    **Content:** `{ errors : "NO UNIQUE PARTY IDENTIFIER PRESENT" }` <br />
    **Reason:** to identify the party, either the :national_id, the :id_card or the :broker_crm_id must be present
    
  OR

  * **Code:** 422 PARTY EXISTS IN OTHER CASE_FILE <br />
    **Content:** `{ errors : "PARTY EXISTS IN OTHER CASE_FILE" }` <br />
    **Reason:** if a party is found which matches one of the unique identifiers, the party will not be created, unless the :force_creation is set to 'true'
    
  OR

  * **Code:** 422 NOT A PARTNER OR OWN_COMPANY <br />
    **Content:** `{ errors : "NOT A PARTNER OR OWN_COMPANY" }` <br />
    **Reason:** when a party is the first party in the case_file, then it must have a :party_role equal to 'partner', 'head_of_family' or 'own_company'
    
  OR

  * **Code:** 422 ALREADY 2 PARTNERS <br />
    **Content:** `{ errors : "ALREADY 2 PARTNERS" }` <br />
    **Reason:** a case_file can only have two parties with a :party_role equal to 'partner' or 'head_of_family'
    
MODEL Party
----

#### Identifiers

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :party_uid  | String | the myFaro UID which is returned to the Application after creation |
| :case_file_uid | String |  |
| :broker_crm_id | String | the user visible party ID in the application is a field which is stored by myFaro and is used as a unique ID to reference the party in the application |
| :broker_crm_group_id | String | a group id which is used to link parties in a casefile in myFaro |
| :national_id | String |  || Content | Content |  |
| :id_card | String |  |

#### Attributes

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :party_role | String | head_of_family (Gezinshoofd - Chef de famille)</br>partner (Partner - Partenaire)</br> community (Gemeenschap - Communauté)</br> third_party (Derde partij - Partie tierce)</br> own_company (Vennootschap - Société)</br> employer (Werkgever - Employeur)</br> child (Kind - Enfant)</br> parent (Ouder - Parent)</br> grandchild (Kleinkind - Petit-enfant)</br> brother_sister (Broer/zus - Frère/sœur)</br> ex_partner (Ex-partner - Ex partenaire)</br> grandparent (Grootouder - Grand-parent)</br> nephew_niece (Neef/nicht - Neveu/nièce)</br> uncle_aunt (Oom/tante - Oncle/tante)</br> party_role_other (Andere - Autre)|
| :gender | String | male (Man - Homme)</br>female (Vrouw - Femme)</br>legal_entity (Rechtspersoon - Personne morale)|
| :first_name | String |  |
| :last_name | String |  |
| :force_creation | Boolean |  |
| :case_file_type | String | license_report (Report)</br> license_view (View)</br> license_insight (Insight)</br> license_insight_plus (Insight+)</br> license_life_plan (Life Plan)|
| :email | String |  |
| :phone | String |  |
| :mobile | String |  |
| :street | String |  |
| :number | String |  |
| :mailbox | String |  |
| :zip_code | String |  |
| :town | String |  |
| :country | String |  |
