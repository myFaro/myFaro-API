## Oauth 2.0

Request your client id and secret from info@myfaro.be.

### GET    /api/v1/fast

Endpoint for fast token validation.

##### curl command

```
curl -H "Authorization: Bearer 53cff8f4a549beb1c38704158b0f6608a2382f094b6947ecc35c2eed4146a17c" \
     <<myfarohost>>/api/v1/fast
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
     <<myfarohost>>/api/v1/me
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
curl --location --request POST 'http://<<myfarohost>>/api/v1/broker/link' \
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

GET CaseFile
---
> **NEW!**
Get the details for a CaseFile using the myFaro uid.  This includes the information on the Parties and Advisors which are assigned to the CaseFile.

* **URL**

  /api/v1/case_files/:uid

* **Method:**

  `GET`
  
*  **URL Params**

   **Required:**
 
   `uid=[string]`

* **Success Response:**

  * **Code:** 200 <br />
    **Content:** `see CaseFile model definition`
 
* **Error Response:**

  * **Code:** 404 CASEFILE NOT FOUND <br />
    **Content:** `{ errors : "CASEFILE NOT FOUND" }`

PUT CaseFile
----
> **NEW!**
Updates a CaseFile, including Advisors.  Parties are added and removed via the CaseFile/Parties relationship.

* **URL**

  /api/v1/case_files/:uid

* **Method:**

  `PUT`
  
*  **URL Params**
 
   `uid=[string]`

* **Data Params**

   `see CaseFile model definition`

* **Success Response:**
  
  * **Code:** 201 <br />
    **Content:** `see CaseFile model definition`
 
* **Error Response:**
  
  * **Code:** 404 CASE FILE NOT FOUND <br />
    **Content:** `{ errors: "CASE FILE NOT FOUND" }`

TODO
----
POST CaseFile/Party<br />
Add a Party to a CaseFile with a certain PartyRole.<br />
DEL CaseFile<br />
Remove a CaseFile<br />
DEL CaseFile/Party<br />
Remove a Party from a CaseFile<br />

GET Party
----
Get the details for a Party using the myFaro uid.

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
    
SEARCH Party
----
Get a list of Parties matching a set of criteria.  Use parameters and pagination to receive 25 parties per call.

* **URL**

  /api/v1/parties/

* **Method:**

  `GET`
  
*  **URL Params**

   `national_id` finds an exact match<br/>
   `id_card` finds an exact match<br/>
   `broker_crm_id` finds an exact match<br/>
   `first_name` finds parties whose first_name contains the parameter<br/>
   `last_name` finds parties whose last_name contains the parameter<br/>
   `page` returns the set of 25 parties contained in this specific page<br/>

* **Success Response:**

  * **Code:** 200 <br/>
    **Content:**
  
       ```
        {"parties": [{see Party model definition},{},...]
        "meta": {`<br/>
             "pagination": {
                 "per_page": 25,
                 "total_pages": 5,
                 "total_objects": 115,
                 "links": {
                     "first": "/api/v1/parties?broker_crm_id=1235&first_name=xyz%C3%A9&id_card=592199787244&last_name=abc%22&national_id=77121307968&page=1",
                     "last": "/api/v1/parties?broker_crm_id=1235&first_name=xyz%C3%A9&id_card=592199787244&last_name=abc%22&national_id=77121307968&page=5",
                     "prev": "/api/v1/parties?broker_crm_id=1235&first_name=xyz%C3%A9&id_card=592199787244&last_name=abc%22&national_id=77121307968&page=2",
                     "next": "/api/v1/parties?broker_crm_id=1235&first_name=xyz%C3%A9&id_card=592199787244&last_name=abc%22&national_id=77121307968&page=4"
                 }
             }
        }
        ```
 
* **Error Response:**

  * **Code:** 404 NO PARTIES FOUND <br />
    **Content:** `{ errors : "NO PARTIES FOUND" }`
  
POST Party
----
Create a new Party.  Also create a CaseFile if it does not exist yet.

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
    
  OR
  
  * **Code:** 404 CASE FILE NOT SAVED <br />
    **Content:** `{ errors: "CASE FILE NOT SAVED" }`
    
PUT Party
----
Update a Party.

* **URL**

  /api/v1/parties/:uid

* **Method:**

  `PUT`
  
*  **URL Params**
 
   `uid=[string]`
   
* **Data Params**

   `see Party model definition`

* **Success Response:**
  
  * **Code:** 201 <br />
    **Content:** `see Party model definition`
 
* **Error Response:**

  * **Code:** 404 PARTY NOT FOUND <br />
    **Content:** `{ errors: "PARTY NOT FOUND" }`

  OR
  
  * **Code:** 404 CASE FILE NOT FOUND <br />
    **Content:** `{ errors: "CASE FILE NOT FOUND" }`

DEL Party
----
> **NEW!**
Delete a Party.  This is only allowed for Parties who are not the key parties in a case_file, don't have any remaining assets and are connected to only one case_file.  

* **URL**

  /api/v1/parties/:uid

* **Method:**

  `DEL`
  
*  **URL Params**
 
   `uid=[string]`

* **Success Response:**
  
  * **Code:** 200 <br />
 
* **Error Response:**

  * **Code:** 404 PARTY NOT FOUND <br />
    **Content:** `{ errors: "PARTY NOT FOUND" }`

  OR
  
  * **Code:** 422 PARTY NOT DELETABLE <br />
    **Content:** `{ errors: "PARTY NOT DELETABLE" }`

GET InsuranceAsset
----
> **NEW!**
Get the details for an Insurance Policy using the myFaro uid.

* **URL**

  /api/v1/insurance_assets/:uid

* **Method:**

  `GET`
  
*  **URL Params**

   **Required:**
 
   `uid=[string]`

* **Success Response:**

  * **Code:** 200 <br />
    **Content:** `see InsuranceAsset model definition`
 
* **Error Response:**

  * **Code:** 404 INSURANCE_ASSET NOT FOUND <br />
    **Content:** `{ errors : "PARTY NOT FOUND" }`
    
POST InsuranceAsset
----
> **NEW!**
Create a new Insurance Policy for an Insuring Party and an Insurance Company.

* **URL**

  /api/v1/insurance_assets

* **Method:**

  `POST`
  
*  **URL Params**
 
   `none`

* **Data Params**

   `see InsuranceAsset model definition`

* **Success Response:**
  
  * **Code:** 201 <br />
    **Content:** `see InsuranceAsset model definition`
 
* **Error Response:**

  * **Code:** 422 NO UNIQUE INSURANCE_ASSET IDENTIFIER PRESENT <br />
    **Content:** `{ errors : "NO UNIQUE INSURANCE_ASSET IDENTIFIER PRESENT" }` <br />
    **Reason:** to identify the insurance_asset, the unique 'policy_reference' must be provided
    
  OR

  * **Code:** 422 NO INSURING PARTY UID PRESENT <br />
    **Content:** `{ errors : "NO INSURING PARTY UID PRESENT" }` <br />
    **Reason:** to identify the party which is holding the InsuranceAsset, the 'insuring_party_uid' must be present
    
  OR

  * **Code:** 422 NO INSURANCE COMPANY IDENTIFIER PRESENT <br />
    **Content:** `{ errors : "NO INSURANCE COMPANY IDENTIFIER PRESENT" }` <br />
    **Reason:** to uniquely identify the InsuranceAsset, the 'regulatory_id' for the insurance company must be present
    
PUT InsuranceAsset
----
> **NEW!**
Update an InsuranceAsset.

* **URL**

  /api/v1/insurance_assets/:uid

* **Method:**

  `PUT`
  
*  **URL Params**
 
   `uid=[string]`
   
* **Data Params**

   `see InsuranceAsset model definition`

* **Success Response:**
  
  * **Code:** 201 <br />
    **Content:** `see InsuranceAsset model definition`
 
* **Error Response:**

  * **Code:** 404 INSURANCE_ASSET NOT FOUND <br />
    **Content:** `{ errors: "INSURANCE_ASSET NOT FOUND" }`


MODEL CaseFile
----
A CaseFile is a set of related Parties.  Each Party has a Role in the CaseFile.  A Party can only have the 'head_of_family_role' or 'partner' role in 1 CaseFile.  A CaseFile is created when creating the first Party.

#### Identifiers

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :case_file_uid  | String | the myFaro UID which is returned to the Application after creation |
| :broker_crm_group_id | String | a group id which is used to link parties in a CaseFile in the application a field which is stored by myFaro and is used as a unique ID to reference the case_file in the application |

#### Attributes

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :case_file_type | String | license_report (Report)</br> license_view (View)</br> license_insight (Insight)</br> license_insight_plus (Insight+)</br> license_life_plan (Life Plan)|
| :advisors | Array | an array of the advisors for the CaseFile.   Each Advisor is represented by a hash.  See Advisor model definition |
| :parties | Array | an array of the parties for the CaseFile.  Each Party is represented by a hash with the following keys: :party_uid, :broker_crm_id, :party_role.  See Party model definition |

MODEL Advisor
----
An Advisor is not a Party in a CaseFile but someone who 'advises' the Parties in some capacity.

#### Attributes

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :name | String |  |
| :advisor_role | String | the role that the Advisor has in this CaseFile:</br>main_advisor (Dossieradviseur - Conseiller de dossier)</br>financial_advisor (Financieel adviseur - Conseiller financier)</br>account_manager (Account manager - Gestionnaire de compte)</br>insurance_advisor (Verzekeringsadviseur - Conseiller assurances)</br>claims_advisor (Claims adviseur	- Conseiller en sinistres)<br>client_executive (Client executive - Client executive)<br>banker (Bankier - Banquier)<br>accountant (Boekhouder/fiscalist - Comptable/Fiscaliste)<br>insurance_broker (Verzekeringsmakelaar - Courtier en assurances)<br>doctor (Geneesheer - Médecin)<br>notary (Notaris - Notaire)<br>advisor_other (Andere - Autre) |
| :email | String |  |
| :phone | String |  |

MODEL Party
----

#### Identifiers

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :party_uid  | String | the myFaro UID which is returned to the Application after creation |
| :broker_crm_id | String | the user visible party ID in the application is a field which is stored by myFaro and is used as a unique ID to reference the party in the application |
| :national_id | String | The national id number for a person or the vat_number for a legal entity |
| :id_card | String |  |

#### Case File Related Attributes

Only to be used when creating a Party to indicate in which CaseFile to create the first instance of the Party - TODO: this will be removed and replaced with a POST case_file and POST case_file/party.  :force_creation then also becomes unnecessary

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :case_file_uid | String | DEPRECATED |
| :broker_crm_group_id | String | a group id which is used to link parties in a CaseFile in the application |
| :party_role | String | the role that the Party has in this CaseFile:</br>head_of_family (Gezinshoofd - Chef de famille)</br>partner (Partner - Partenaire)</br> community (Gemeenschap - Communauté)</br> third_party (Derde partij - Partie tierce)</br> own_company (Vennootschap - Société)</br> employer (Werkgever - Employeur)</br> child (Kind - Enfant)</br> parent (Ouder - Parent)</br> grandchild (Kleinkind - Petit-enfant)</br> brother_sister (Broer/zus - Frère/sœur)</br> ex_partner (Ex-partner - Ex partenaire)</br> grandparent (Grootouder - Grand-parent)</br> nephew_niece (Neef/nicht - Neveu/nièce)</br> uncle_aunt (Oom/tante - Oncle/tante)</br> party_role_other (Andere - Autre)|
| :force_creation | Boolean |  |

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :case_file_type | String | license_report (Report)</br> license_view (View)</br> license_insight (Insight)</br> license_insight_plus (Insight+)</br> license_life_plan (Life Plan)|
| :case_files | Array | an array of hashes with the following keys: :broker_crm_group_id, :case_file_uid, :party_role |

#### Attributes

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :gender | String | male (Man - Homme)</br>female (Vrouw - Femme)</br>legal_entity (Rechtspersoon - Personne morale)|
| :first_name | String |  |
| :last_name | String |  |
| :date_of_birth | Date |  |
| :date_of_death | Date |  |
| :place_of_birth | String |  |
| :email | String |  |
| :phone | String |  |
| :mobile | String |  |
| :street | String |  |
| :number | String |  |
| :mailbox | String |  |
| :zip_code | String |  |
| :town | String |  |
| :country | String | ISO-3166-1-alpha-2 |
| :locale | String | ISO-639-1 The stated language will be used to determine the language of the case_file at the time of creation - once a case_file is created, the language cannot be changed |
| :civil_status | String | telebib	A123 |
| :social_status | String | telebib A132 |
| :profession | String |  |
| :id_valid_from | Date |  |
| :id_valid_until | Date |  |

MODEL InsuranceAsset
----
An InsuranceAsset is a Life Insurance Policy.  The unique key for an InsuranceAsset is the combination of the 'policy_reference' and the Insurance Company, which is identified with the 'regularory_id'.

#### Identifiers

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :insurance_asset_uid  | String | the myFaro UID which is returned to the Application after creation |
| :PolicyReference | String | a unique identifier used by the issuing insurance company; versioning pre- or suffixes should not be used as we don't keep versions for the same policy |
| :insurance_company_regulatory_id | String | the unique identifier issued by the national regulator (FSMA in BE or CAA in LUX) |
| :insuring_party_uid | the myFaro UID of the Party which holds or subscribes to the policy | 

#### Attributes

Policy as per https://www.telebib2.org/MIGFrameset.asp?MigId=815&lang=n

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :PolicyProductcode  | String | Polistype |
| :ProductNameProductcode  | String | Product |
| :ContractInceptionDate  | Date | Initiële aanvangsdatum van het contract |
| :ExpiryDate  | String | Afloopdatum |
| :ContractStatusCode  | String | Status van de polis |
| :MonetaryUnitCode  | String | Munteenheid |
| :DegreeOfTaxExemptionCode  | String | Graad van vrijstelling van taks |
| :MortgageClausePresenceBinary  | Boolean | Aanwezigheid hypotheekclausule |
| :PledgedContractBinary  | Boolean | Contract in onderpand |
| :BasePremiumAmount | Float | Technische premie of zuivere premie |
| :NetPremiumAmount  | Float | Netto premie |
| :GrossPremiumAmount  | Float | Bruto premie |
| :  | String |  |
	
	  	
	  	
CLS+002 - ( Tekstuele clausule ) -(0..n) -(Min. 33% of data expected) - (ClauseLiteral)
122
	
1
	  	
RFF+031 - ( Nummer clausule ) -(0..1) - (ClauseReference)
123
	
1
	  	
FTX+052 - ( Clausule, titel ) -(1..1) - (ClauseTitleText)
124
	
1
	  	
FTX+053 - ( Clausule, tekst ) -(1..n) - (ClauseTextText)
125
	
1
	  	
XRT+1 - ( ) -(1..1) - ()
126
	
1
	  	
XRH+1 - ( ) -(1..1) - ()
127
	
1
	  	
CLS+010 - ( IPID (KID non-life) ) -(0..n) - (ClauseIPIDIsKIDInNonLife)
128
	
1
	  	
RFF+110 - ( Referte IPID (KID non-life) ) -(1..1) - (IPIDAsIsKIDNonLifeReference)
129
	
1
	  	
XRT+1 - ( ) -(1..1) - ()
130
	
1
	  	
XRH+1 - ( ) -(1..1) - ()
131
	
1
	  	
CLS+011 - ( KID (Life/Investment) ) -(0..n) - (ClauseKIDInLifeInvestment)
132
	
1
	  	
RFF+111 - ( Referte KID (Life/Investment) ) -(1..1) - (KIDInLifeAndinvestmentReference)
133
	
1
	  	
XRT+1 - ( ) -(1..1) - ()
134
	
1
	  	
XRH+1 - ( ) -(1..1) - ()
135
	
1
	  	
PTY+006 - ( Verzekeraar ) -(1..1) - (PartyInsurer)
136
	
1
	  	
RFF+075 - ( Ondernemingsnummer ) -(0..1) - (CompanyNumberReference)
137
	
1
	  	
RFF+076 - ( Vestigingseenheidsnummer ) -(0..1) - (EstablishmentUnitNumberReference)
138
	
1
	  	
RFF+107 - ( LEI ) -(0..1) - (LegalEntityIdentifierReference)
139
	
1
	  	
ATT+A020 - ( Type maatschappij bij medeverzekering ) -(0..1) - (InsurerInCaseOfCoInsuranceCode)
140
	
1
	  	
XRT+1 - ( ) -(1..1) - ()
141
	
1
	  	
XRH+1 - ( ) -(1..1) - ()
142
	
1
	  	
PTY+002 - ( Tussenpersoon ) -(1..1) - (PartyIntermediary)
143
	
1
	  	
RFF+075 - ( Ondernemingsnummer ) -(0..1) - (CompanyNumberReference)
144
	
1
	  	
RFF+076 - ( Vestigingseenheidsnummer ) -(0..1) - (EstablishmentUnitNumberReference)
145
	
1
	  	
RFF+107 - ( LEI ) -(0..1) - (LegalEntityIdentifierReference)
146
	
1
	  	
RFF+003 - ( Referte polis tussenpersoon ) -(0..1) - (BrokerPolicyReference)
147
	
2
	  	
XRH+2 - ( ) -( 1..1) - ()
148
	
2
	  	
RPT+210 - ( Correspondent ) -( 0..1) - (RelatedCorrespondentOrManager)
149
	
2
	  	
NME+001 - ( Naam ) -( 0..1) - (OfficialName)
150
	
2
	  	
COM+006 - ( Telefoon bureau ) -( 0..1) - (OfficeTelephoneCommID)
151
	
2
	  	
COM+002 - ( Fax ) -( 0..1) - (FaxCommID)
152
	
2
	  	
COM+003 - ( e-Mail ) -( 0..1) - (EmailAddressCommID)
153
	
2
	  	
XRT+2 - ( ) -( 1..1) - ()
154
	
1
	  	
XRT+1 - ( ) -(1..1) - ()
155
	
1
	  	
XRH+1 - ( ) -(1..1) - ()
156
	
1
	  	
PTY+003 - ( Verzekeringsnemer ) -(1..1) -(Extendible with one of: PTY003PP_202201, PTY003PM_202201, PTY003Grp_202201) - (PartyMainPolicyHolder)
157
	
1
	  	
XRT+1 - ( ) -(1..1) - ()
158
	
1
	  	
XRH+1 - ( ) -(1..1) - ()
159
	
1
	  	
PTY+017 - ( Medeverzekeringsnemer ) -(0..1) -(Extendible with one of: PTY003PP_202201) - (PartyAdditionalPolicyHolder)
160
	
1
	  	
XRT+1 - ( ) -(1..1) - ()
161
	
1
	  	
XRH+1 - ( ) -(1..1) - ()
162
	
1
	  	
ROD+xxx - ( Object ) -(1..n) -(Extendible with one of: RODGen_202201) - ()
163
	
1
	  	
XRT+1 - ( ) -(1..1) - ()
164
	
0
	
XET+01 - ( Contract ) - (1..1) - (ContractExchangeUnitTrailer)





Insured Parties as per https://www.telebib2.org/MIGFrameset.asp?MigId=805&lang=n (COMMON) and https://www.telebib2.org/MIGFrameset.asp?MigId=803&lang=n (PERSON)

| Attributes  | Type | Notes |
| :--- | :--- | :--- |

Coverage Objects as per https://www.telebib2.org/MIGFrameset.asp?MigId=813&lang=n (COMMON), https://www.telebib2.org/MIGFrameset.asp?MigId=811&lang=n (LIFE) and https://www.telebib2.org/MIGFrameset.asp?MigId=665&lang=n (DISABILITY)

| Attributes  | Type | Notes |
| :--- | :--- | :--- |

MODEL ActionItem - TO BE IMPLEMENTED
----
An action item is an opportunity or a administrative task connected to a party.  The combination of a party and an action_item_key is unique.

#### Identifiers

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :party_uid  | String | the myFaro UID for a party |
| :action_item_key | String | a unique identifier for the type of action |

#### Attributes

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :action_type | String | </br> info</br> process</br> analyse</br> optimise</br> execute |
| :action_name | String | name of the opportunity or administrative task in the party's locale |
| :action_description | String | description of the opportunity or administrative task in the party's locale |
| :status_level | String | indication of the level of existing coverage:</br> empty</br> half</br> full
| :completed_at | Date | date on which the action was completed in myFaro |

OTHER
----

### Access myFaro using access token

Once a user has linked their application account with a myFaro account via Oauth, they can use the access token to login into myFaro and access myFaro pages based on their myFaro user rights.  The access token is only valid for 5 minutes.

For instance, to show the case_file in myFaro:

```
http://<<myfarohost>>/case_files/5c44c7981143ec0080551ec3?locale=nl&parm_limit=0&access_token=68595f50b8d7184af3357a29af79824b22c31e49cfff7f78d7c6469feb3cfa99
```
