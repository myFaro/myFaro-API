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
    "user_uid": "5e021e7c5eb6f54eef65e293",
    "user_name": "John Doe",
    "user_email": "john.doe@gmail.com",
    "roles": {
        "role_advisor": true,
        "role_administrator": true,
        "role_compliance": true,
        "role_security": true
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

## Token Access

### Access myFaro using access OAuth token

Once a user has linked their application account with a myFaro account via Oauth, they can use the access token to login into myFaro and access myFaro pages based on their myFaro user rights.  The access token is only valid for 5 minutes.

For instance, to show the case_file in myFaro:

```
http://<<myfarohost>>/case_files/5c44c7981143ec0080551ec3?locale=nl&parm_limit=0&access_token=68595f50b8d7184af3357a29af79824b22c31e49cfff7f78d7c6469feb3cfa99
```


### Access myFaro using JWT token

You can ask for a JWT token to allow a Party to login to myFaro and/or request a resource from myFaro.  The user does not need to have linked their account using OAuth.

### POST /api/v1/authorized_resources/token

* **URL**

  /api/v1/authorized_resources/token

* **Method:**

  `POST`
  
*  **URL Params**

   **Required:**
 
   `party_uid=[string]`
   
   **Optional:**
 
   `resource=[string]`
    
   When a Resource is provided, the User will be redirected to this Resource after succesfull login.  If no Resource or a non-supported Resource is provided, the User will be redirected to the CaseFile's default interface.  The following list shows the supported Resources:
   
   | Resource | Notes |
   | :--- | :--- |
  

* **Success Response:**

  * **Code:** 201 SUCCESS<br />
    **Content:** `"uri": "http://<<myfarohost>>/authorized_resource?locale=nl&token=eyJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MjYzNjEwNjEsImlzcyI6Im15RmFybyIsImF1ZCI6Im15RmFybyIsInVzZXJfaWQiOiI2MGYwNDg0NGIzZjAzNjBjOGI0NjM4YzUifQ.lhzctMaiyvbPfIQjLJPITphVwT7GX9KFhUSI23T5Sds"`
 
* **Error Response:**

  * **Code:** 403 TOKEN CANNOT BE GENERATED<br />
    
  * **Code:** 404 PARTY NOT FOUND<br />

## CaseFile

### GET CaseFile

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

### PUT CaseFile

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

### TODO

POST CaseFile/Party<br />
Add a Party to a CaseFile with a certain PartyRole.<br />
DEL CaseFile<br />
Remove a CaseFile<br />
DEL CaseFile/Party<br />
Remove a Party from a CaseFile<br />

## Party

### GET Party

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
    
### SEARCH Party

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
  
### POST Party

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
    
### PUT Party

Update a Party.

* **URL**

  /api/v1/parties/:uid

* **Method:**

  `PUT`
  
*  **URL Params**
    
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

### DEL Party

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

## InsuranceAsset

### GET InsuranceAsset

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
    
### POST InsuranceAsset

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
    
### PUT InsuranceAsset

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

### GET InsuranceAssets

Get a list of InsuranceAssets matching a set of criteria.  Use parameters and pagination to receive 25 InsuranceAssets per call.

* **URL**

  /api/v1/insurance_assets/

* **Method:**

  `GET`
  
*  **URL Params**

   `fsma_reference` limit the insurance assets for a certain insurance company<br/>
   `insuring_party_uid` limit the insurance assets for a certain party as insurance policy holder<br/>
   `insured_party_uid` limit the insurance assets for a certain party as insurance policy insured party<br/>
   `page` returns the set of 25 InsuranceAssets contained in this specific page<br/>

* **Success Response:**

  * **Code:** 200 <br/>
    **Content:**
  
       ```
        {"insurance_assets": [{see Insurance Asset model definition},{},...]
        "meta": {`<br/>
             "pagination": {
                 "per_page": 25,
                 "total_pages": 5,
                 "total_objects": 115,
                 "links": {
                	"first": "/api/v1/insurance_assets?fsma_reference=79&insured_party_uid=&insuring_party_uid=&page=1",
                	"last": "/api/v1/insurance_assets?fsma_reference=79&insured_party_uid=&insuring_party_uid=&page=2",
                	"next": "/api/v1/insurance_assets?fsma_reference=79&insured_party_uid=&insuring_party_uid=&page=2"
            	}
             }
        }
        ```
 
* **Error Response:**

  * **Code:** 404 NO INSURANCE ASSETS FOUND <br />


## Documents

### POST Documents

Upload a document for an entity.

* **URL**

   /api/v1/case_files/:uid/documents<br />
   /api/v1/parties/:uid/documents<br />
   /api/v1/insurance_assets/:uid/documents<br />

* **Method:**

  `POST`
  
*  **URL Params**
 
   `uid=[string]`
   
* **Data Params**

   :document - a base64 encoded file

* **Success Response:**
  
  * **Code:** 201 <br />
    **Content:** `DOCUMENT SAVED`
 
* **Error Response:**

  * **Code:** 404 ENTITY NOT FOUND <br />
    **Content:** `{ errors: "ENTITY NOT FOUND" }`

  * **Code:** 422 NO DOCUMENT PRESENT <br />
    **Content:** `{ errors: "NO DOCUMENT PRESENT" }`
  
  * **Code:** 422 DOCUMENT NOT SAVED <br />
    **Content:** `{ errors: "DOCUMENT NOT SAVED" }`

## MODELS

### MODEL CaseFile

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

### MODEL Advisor

An Advisor is not a Party in a CaseFile but someone who 'advises' the Parties in some capacity.

#### Attributes

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :name | String |  |
| :advisor_role | String | the role that the Advisor has in this CaseFile:</br>main_advisor (Dossieradviseur - Conseiller de dossier)</br>financial_advisor (Financieel adviseur - Conseiller financier)</br>account_manager (Account manager - Gestionnaire de compte)</br>insurance_advisor (Verzekeringsadviseur - Conseiller assurances)</br>claims_advisor (Claims adviseur	- Conseiller en sinistres)<br>client_executive (Client executive - Client executive)<br>banker (Bankier - Banquier)<br>accountant (Boekhouder/fiscalist - Comptable/Fiscaliste)<br>insurance_broker (Verzekeringsmakelaar - Courtier en assurances)<br>doctor (Geneesheer - Médecin)<br>notary (Notaris - Notaire)<br>advisor_other (Andere - Autre) |
| :email | String |  |
| :phone | String |  |

### MODEL Party

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

### MODEL InsuranceAsset

An InsuranceAsset is a Life Insurance Policy.  The unique key for an InsuranceAsset is the combination of the 'policy_reference' and the Insurance Company, which is identified with the 'regularory_id'.

#### Identifiers

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :insurance_asset_uid  | String | the myFaro UID which is returned to the Application after creation |
| :PolicyReference | String | a unique identifier used by the issuing insurance company; versioning pre- or suffixes should not be used as we don't keep versions for the same policy |
| :FsmaReference | String | the unique identifier issued by the national regulator (FSMA in BE or CAA in LUX) |

#### Attributes

Policy as per https://www.telebib2.org/MIGFrameset.asp?MigId=815&lang=n

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :PolicyInfo  | Hash | See PolicyInfo |
| :InsuredParties | Array | array of {party_uid: myFaro UID, first_name: firstname, last_name: lastname} for the Parties that are insured by the policy |
| :Guarantees | Array | see Guarantees |

#### PolicyInfo

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
| :PartyMainPolicyHolder | Hash | {party_uid: myFaro UID, first_name: firstname, last_name: lastname} of the Party which holds or subscribes to the policy Verzekeringsnemer |
| :PartyAdditionalPolicyHolder | Hash | {party_uid: myFaro UID, first_name: firstname, last_name: lastname} of the Party which holds or subscribes to the policy Medeverzekeringsnemer |

#### Guarantees 

https://www.telebib2.org/MIGFrameset.asp?MigId=813&lang=n (COMMON)

https://www.telebib2.org/MIGFrameset.asp?MigId=811&lang=n (LIFE)

https://www.telebib2.org/MIGFrameset.asp?MigId=665&lang=n (DISABILITY)

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :InsuredParty | Hash | {party_uid: myFaro UID, first_name: firstname, last_name: lastname} of the InsuredParty | 
| :LifeGuaranteeType | String | Type waarborg leven https://www.telebib2.org/popupListsBis.asp?waarde=A574 |
| :InceptionDate | Date | Aanvangsdatum |
| :ExpiryDate | Date | Afloopdatum |
| :ExemptionPresentBinary | Boolean | Vrijstelling |
| :FranchiseDeductableBuyoutBinary | Boolean | Afkoop vrijstelling |
| :BasePremiumAmount | Float | Technische premie of zuivere premie |
| :NetPremiumAmount  | Float | Netto premie |
| :GrossPremiumAmount  | Float | Bruto premie |
| :LifeBenefitPaymentDate | Date | Uitkeringsdatum kapitaal leven |
| :ReserveValuationDate | Date | Datum waardering reserve |
| :ReportedAsOfDate | Date | Datum prognoses |
| :FiscalEndCapitalCalculusDate | Date | Berekeningsdatum fiscaal eindkapitaal |
| :DebtBalanceCode | String | Type schuldsaldo |
| :PerformanceTaxabilityCode | String | Fiscaal regime van de prestatie |
| :FiscalStatusOfTheLifeInsurancePremiumCode | String | Fiscaliteit levensverzekeringspremie |
| :YieldGuaranteeCode | String | Rendementsgarantie |
| :RiskLabelCode | String | Risico label |
| :PaymentForLongTermDisabilityCode | String | Type uitkering voor langdurige invaliditeit |
| :IncapacityCauseCoveredCode | String | Gedekte oorzaak ongeschiktheid |
| :IncapacityCoveredCode | String | Gedekt type ongeschiktheid |
| :AdvanceOnPolicyBinary | Float | Voorschot op polis |
| :AdvanceOnAiirGuaranteeBinary | Float | Voorschot op waarborg AVRI |
| :ContractGeneratesProfitsharingBinary | Boolean | Contract genereert winstdeelname |
| :PerformanceTaxationLiabilityBinary | String | Onderworpenheid van de prestatie |
| :MinimumAmountInCaseOfDeathBinary | Float | Minimaal bedrag in overlijden |
| :SupplementalAmountInCaseOfDeathBinary | Float | Bijkomend bedrag in overlijden |
| :DeathBenefitDecreasingBinary | Boolean | Afnemend overlijdenskapitaal |
| :DeathBenefitCalculatedBinary | Boolean | Overlijdenskapitaal berekend in functie van gestorte premies |
| :PartialDisabilityCoveredBinary | String | Gedeeltelijke ongeschiktheid gedekt |
| :LifeBenefitAmount | Float | Kapitaal bij leven |
| :ReserveValueAmount | Float | Waarde van de reserve |
| :AcquiredBonusLoadingForLifeAmount | Float | Verworven winstdeelname bij leven |
| :DeathBenefitAmount | Float | Kapitaal bij overlijden |
| :SupplementalAmountInCaseOfDeathAmount | Float | Bijkomend bedrag bij overlijden |
| :YearlyInterestInLifeInsuranceAmount | Float | Jaarlijkse rente (Levensverzekering) |
| :CautiousPrognosisFinalCapitalAmount | Float | Voorzichtige pronostiek eindkapitaal |
| :NormalPrognosisFinalCapitalAmount | Float | Normale pronostiek eindkapitaal |
| :OptimisticPrognosisFinalCapitalAmount | Float | Optimistische pronostiek eindkapitaal |
| :EndCapitalFiscallyNoPremiumsAmount | Float | Fiscaal kapitaal zonder premies |
| :EndCapitalFiscallyPremiumsPaidAmount | Float | Fiscaal kapitaal met premies |
| :AIARMultiplicatorQuantity | Float | Vermenigvuldigingscoëfficiënt AVRO |
| :RateOnRemainingBalanceDuedPercentage | Float | Intrestpercentage schuldsaldo |
| :InterventionAsOfIncapacityPercentage | Float | Tussenkomst zodra percentage ongeschiktheid |
| :PermanentIncapacityTresholdPercentage | Float | Drempel totale blijvende invaliditeit |
| :GrowthOfTheAllowancePercentage | Float | Rente invaliditeit - aangroeipercentage |
| :YearlyGrowthOfTheAllowancePercentage | Float | Rente invaliditeit - jaarlijks aangroeipercentage |
| :GuaranteedInterestRateLifePercentage | Float | Gewaarborgde rentevoet leven |
| :GuaranteedInterestRateOnReservePercentage | Float | Gewaarborgde rentevoet op de reserve |
| :RateOfBonusLoadingInCaseOfLifePercentage | Float | Percentage winstdeelname (leven) |
| :BonusLoadingInCaseOfDeathPercentage | Float | Percentage winstdeelname (overlijden) |
| :CompensationDuration | Float | Duur vergoeding |
| :Beneficiaries | Array | See Beneficiaries |
| :Funds | Array | See Funds |
	
#### Beneficiaries

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :PartyBeneficiary | Hash | {party_uid: myFaro UID, first_name: firstname, last_name: lastname} of the Party |
| :StandardBeneficiaryCode | String | Type standaardbegunstigde |
| :BeneficiaryRankOrderCode | String | Type aannemende begunstigde |
| :BeneficiaryAcceptedTheContractualBenefitBinary | Boolean | Begunstigde heeft contractvoordeel aanvaard |
| :BeneficiaryAmount | Float | Begunstigde - bedrag |
| :BeneficiaryQuotaPercentage | Float | Begunstigde - quotum |

#### Funds

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :InventoryValuationDate | Date | Datum inventariswaarde |
| :IsinCodeReference | String | ISIN-code |
| :FundReferenceCompanyReference | String | Referte fonds (maatschappij) |
| :FinancialProfilingCode | String | Financiële profilering |
| :FundStopLossCode | String | Stop Loss bescherming |
| :RiskLabelCode | String | Risico label |
| :InventoryFundUnitValueAmount | Float | Waarde per eenheid van het fonds bij aankoop |
| :ValueAsOnInventoryDateAmount | Float | Waarde op de inventarisdatum |
| :NubmerOfUnitsOfTheFundQuantity | Float | Aantal eenheden van het fonds |
| :PercentageBoardingFeeAdditionalDepositsPercentage | Float | Percentage instapkosten aanvullende storting |
| :FundNameText | String | Naam van het fonds |

### MODEL ActionItem - TO BE IMPLEMENTED

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

## Webhooks

### POST    /api/v1/webhooks/<application>

Provide myFaro with a trigger to start processing an action for one or more objects of a certain object class.   Each supported application has its own endpoint.   For instance /api/v1/webhooks/brokercloud

#### Applications supported
| Application |
| :--- |
| brokercloud |

#### Attributes

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :object_class | String | broker </br> party </br> mifid_file |
| :object_uids | Array of String | unique uid of the objects to be processed |
| :action | String | create </br> update </br> destroy |
