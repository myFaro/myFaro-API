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
| :FsmaReference | String | the unique identifier issued by the national regulator (FSMA in BE or CAA in LUX) |

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
| :PartyInsurer  | String | Verzekeraar |
| :CompanyNumberReference  | String | Ondernemingsnummer van de verzekeraar |
| :PartyMainPolicyHolder | String | the myFaro UID of the Party which holds or subscribes to the policy Verzekeringsnemer |
| :PartyAdditionalPolicyHolder | String | the myFaro UID of the Party which holds or subscribes to the policy Medeverzekeringsnemer |
| :InsuredParties | Array | array of the myFaro UID of the Parties that are insured by the policy |
| :Guarantees | Array | see Guarantees |

Guarantees 
https://www.telebib2.org/MIGFrameset.asp?MigId=813&lang=n (COMMON)
https://www.telebib2.org/MIGFrameset.asp?MigId=811&lang=n (LIFE)
https://www.telebib2.org/MIGFrameset.asp?MigId=665&lang=n (DISABILITY)

| Attributes  | Type | Notes |
| :--- | :--- | :--- |
| :LifeGuaranteeType | String | Type waarborg leven |
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
| : | String |  |
| : | String |  |


	
ATT+2200 - ( Type uitkering voor langdurige invaliditeit ) - (0..1) - (PaymentForLongTermDisabilityCode)
18
	
0
	
ATT+2260 - ( Gedekte oorzaak ongeschiktheid ) - (0..1) - (IncapacityCauseCoveredCode)
19
	
0
	
ATT+2262 - ( Gedekt type ongeschiktheid ) - (0..1) - (IncapacityCoveredCode)
20
	
0
	
BIN+A063 - ( Voorschot op polis ) - (0..1) - (AdvanceOnPolicyBinary)
21
	
0
	
BIN+A064 - ( Voorschot op waarborg AVRI ) - (0..1) - (AdvanceOnAiirGuaranteeBinary)
22
	
0
	
BIN+1010 - ( Contract genereert winstdeelname ) - (0..1) - (ContractGeneratesProfitsharingBinary)
23
	
0
	
BIN+1020 - ( Contract onderhevig aan perequatie ) - (0..1) - (ContractSubjectToEqualizationBinary)
24
	
0
	
BIN+1405 - ( Onderworpenheid van de prestatie ) - (0..1) - (PerformanceTaxationLiabilityBinary)
25
	
0
	
BIN+1202 - ( Minimaal bedrag in overlijden ) - (0..1) - (MinimumAmountInCaseOfDeathBinary)
26
	
0
	
BIN+1203 - ( Bijkomend bedrag in overlijden ) - (0..1) - (SupplementalAmountInCaseOfDeathBinary)
27
	
0
	
BIN+1204 - ( Bijkomend bedrag eerst toegepast ) - (0..1) - (SupplementalAmountAppliedFirstBinary)
28
	
0
	
BIN+1205 - ( Afnemend overlijdenskapitaal ) - (0..1) - (DeathBenefitDecreasingBinary)
29
	
0
	
BIN+1206 - ( Overlijdenskapitaal berekend in functie van gestorte premies ) - (0..1) - (DeathBenefitCalculatedBinary)
30
	
0
	
BIN+1207 - ( Overlijdenskapitaal anders vastgelegd ) - (0..1) - (DeathBenefitDefinedDifferentlyBinary)
31
	
0
	
BIN+2261 - ( Gedeeltelijke ongeschiktheid gedekt ) - (0..1) - (PartialDisabilityCoveredBinary)
32
	
0
	
BIN+1625 - ( Lijfrente overgaand op 2de hoofd ) - (0..1) - (AnnuityTransposingTo2ndHeadBinary)
33
	
0
	
MOA+023 - ( Kapitaal bij leven ) - (0..1) - (LifeBenefitAmount)
34
	
0
	
MOA+186 - ( Waarde van de reserve ) - (0..1) - (ReserveValueAmount)
35
	
0
	
MOA+025 - ( Toekenning winstdeelname leven van het jaar ) - (0..1) - (YearlyAmountOfBonusLoadingForLifeAmount)
36
	
0
	
MOA+024 - ( Verworven winstdeelname bij leven ) - (0..1) - (AcquiredBonusLoadingForLifeAmount)
37
	
0
	
MOA+093 - ( Wijziging kapitaal leven ) - (0..1) - (ChangeInLifeBenefitAmount)
38
	
0
	
MOA+026 - ( Kapitaal bij overlijden ) - (0..1) - (DeathBenefitAmount)
39
	
0
	
MOA+028 - ( Toekenning winstdeelname overlijden van het jaar ) - (0..1) - (YearlyAmountOfBonusLoadingForDeathAmount)
40
	
0
	
MOA+027 - ( Verworven winstdeelname bij overlijden ) - (0..1) - (AcquiredBonusLoadingForDeathAmount)
41
	
0
	
MOA+094 - ( Wijziging kapitaal overlijden ) - (0..1) - (ChangeInDeathBenefitAmount)
42
	
0
	
MOA+180 - ( Jaarlijkse rente (Levensverzekering) ) - (0..1) - (YearlyInterestInLifeInsuranceAmount)
43
	
0
	
MOA+042 - ( Kapitaal - Jaarvergoeding ) - (0..1) - (CapitalAnnualCompensationAmount)
44
	
0
	
MOA+191 - ( Bijkomend bedrag bij overlijden ) - (0..1) - (SupplementalAmountInCaseOfDeathAmount)
45
	
0
	
MOA+218 - ( Voorzichtige pronostiek eindkapitaal ) - (0..1) - (CautiousPrognosisFinalCapitalAmount)
46
	
0
	
MOA+219 - ( Normale pronostiek eindkapitaal ) - (0..1) - (NormalPrognosisFinalCapitalAmount)
47
	
0
	
MOA+220 - ( Optimistische pronostiek eindkapitaal ) - (0..1) - (OptimisticPrognosisFinalCapitalAmount)
48
	
0
	
MOA+221 - ( Fiscaal kapitaal zonder premies ) - (0..1) - (EndCapitalFiscallyNoPremiumsAmount)
49
	
0
	
MOA+222 - ( Fiscaal kapitaal met premies ) - (0..1) - (EndCapitalFiscallyPremiumsPaidAmount)
50
	
0
	
MOA+223 - ( Afkoopwaarde ) - (0..1) - (SurrenderValueAmount)
51
	
0
	
MOA+224 - ( Kapitaal leven als theoretische rente ) - (0..1) - (LifeCapitalConvertedIntoTheoreticalAnnuityAmount)
52
	
0
	
QTY+129 - ( Vermenigvuldigingscoëfficiënt AVRO ) - (0..1) - (AIARMultiplicatorQuantity)
53
	
0
	
PCD+008 - ( Intrestpercentage schuldsaldo ) - (0..1) - (RateOnRemainingBalanceDuedPercentage)
54
	
0
	
PCD+012 - ( Tussenkomst zodra percentage ongeschiktheid ) - (0..1) - (InterventionAsOfIncapacityPercentage)
55
	
0
	
PCD+072 - ( Drempel totale blijvende invaliditeit ) - (0..1) - (PermanentIncapacityTresholdPercentage)
56
	
0
	
PCD+044 - ( Rente invaliditeit - aangroeipercentage ) - (0..1) - (GrowthOfTheAllowancePercentage)
57
	
0
	
PCD+070 - ( Rente invaliditeit - jaarlijks aangroeipercentage ) - (0..1) - (YearlyGrowthOfTheAllowancePercentage)
58
	
0
	
PCD+057 - ( Gewaarborgde rentevoet leven ) - (0..1) - (GuaranteedInterestRateLifePercentage)
59
	
0
	
PCD+058 - ( Gewaarborgde rentevoet op de reserve ) - (0..1) - (GuaranteedInterestRateOnReservePercentage)
60
	
0
	
PCD+030 - ( Percentage winstdeelname (leven) ) - (0..1) - (RateOfBonusLoadingInCaseOfLifePercentage)
61
	
0
	
PCD+041 - ( Percentage winstdeelname (overlijden) ) - (0..1) - (BonusLoadingInCaseOfDeathPercentage)
62
	
0
	
PCD+062 - ( Percentage van de netto investering ) - (0..1) - (TheNetInvestmentPercentage)
63
	
0
	
PCD+063 - ( Percentage van de herbelegging ) - (0..1) - (TheReInvestmentPercentage)
64
	
0
	
PCD+064 - ( Waarborg overlijden in percentage assets ) - (0..1) - (DeathCoverPerAssetsPercentage)


3
	
0
	
DTM+031 - ( Duur vergoeding ) - (0..1) - (CompensationDuration)
4
	
0
	
ATT+2200 - ( Type uitkering voor langdurige invaliditeit ) - (0..1) - (PaymentForLongTermDisabilityCode)
5
	
0
	
ATT+2260 - ( Gedekte oorzaak ongeschiktheid ) - (0..1) - (IncapacityCauseCoveredCode)
6
	
0
	
ATT+2262 - ( Gedekt type ongeschiktheid ) - (0..1) - (IncapacityCoveredCode)
7
	
0
	
BIN+21V9 - ( Overeengekomen indeplaatsstelling ) - (0..1) - (AgreedSubrogationBinary)
8
	
0
	
BIN+2261 - ( Gedeeltelijke ongeschiktheid gedekt ) - (0..1) - (PartialDisabilityCoveredBinary)
9
	
0
	
BIN+1625 - ( Lijfrente overgaand op 2de hoofd ) - (0..1) - (AnnuityTransposingTo2ndHeadBinary)
10
	
0
	
MOA+026 - ( Kapitaal bij overlijden ) - (0..1) - (DeathBenefitAmount)
11
	
0
	
MOA+036 - ( Kapitaal - Dagvergoeding ) - (0..1) - (CapitalDailyAllowanceAmount)
12
	
0
	
MOA+037 - ( Kapitaal - Maximale uitkering ) - (0..1) - (CapitalMaximumBenefitAmount)
13
	
0
	
MOA+042 - ( Kapitaal - Jaarvergoeding ) - (0..1) - (CapitalAnnualCompensationAmount)
14
	
0
	
PCD+012 - ( Percentage tussenkomst ) - (0..1) - (InterventionAsOfIncapacityPercentage)
15
	
0
	
PCD+072 - ( Drempel totale blijvende invaliditeit ) - (0..1) - (PermanentIncapacityTresholdPercentage)
16
	
0
	
PCD+044 - ( Stijgingspercentage van de rente ) - (0..1) - (GrowthOfTheAllowancePercentage)
17
	
0
	
PCD+070 - ( Rente - jaarlijks aangroeipercentage ) - (0..1) - (YearlyGrowthOfTheAllowancePercentage)
18
	


BEGUNSTIGDEN

66
	
1
	  	
PTY+014 - ( Begunstigde ) -(0..n) -(Min. 40% of data expected) - (PartyBeneficiary)
67
	
1
	  	
NME+001 - ( Officiële naam ) -(0..1) - (OfficialName)
68
	
1
	  	
ADR+002 - ( Adres ) -(0..1) - (OfficialAddress)
69
	
1
	  	
ATT+1910 - ( Type standaardbegunstigde ) -(0..1) - (StandardBeneficiaryCode)
70
	
1
	  	
ATT+1912 - ( Rang ) -(1..1) - (BeneficiaryRankOrderCode)
71
	
1
	  	
ATT+1A10 - ( Type aannemende begunstigde ) -(0..1) - (AcceptingBeneficiaryCode)
72
	
1
	  	
BIN+1911 - ( Begunstigde heeft contractvoordeel aanvaard ) -(0..1) - (BeneficiaryAcceptedTheContractualBenefitBinary)
73
	
1
	  	
MOA+225 - ( Begunstigde - bedrag ) -(0..1) - (BeneficiaryAmount)
74
	
1
	  	
PCD+073 - ( Begunstigde - quotum ) -(0..1) - (BeneficiaryQuotaPercentage)
75
	
1
	  	
FTX+019 - ( Beschrijving ) -(0..1) - (DescriptionText)
76
	
1
	  	
XRT+3 - ( ) -(1..1) - ()
77
	
1
	  	
XRH+3 - ( ) -(1..1) - ()
78
	
1
	  	
PTY+048 - ( Begunstigde bij leven ) -(0..n) -(Min. 40% of data expected) - (PartyBeneficiaryInCaseOfLife)
79
	
1
	  	
NME+001 - ( Officiële naam ) -(0..1) - (OfficialName)
80
	
1
	  	
ADR+002 - ( Adres ) -(0..1) - (OfficialAddress)
81
	
1
	  	
ATT+1910 - ( Type standaardbegunstigde ) -(0..1) - (StandardBeneficiaryCode)
82
	
1
	  	
ATT+1912 - ( Rang ) -(1..1) - (BeneficiaryRankOrderCode)
83
	
1
	  	
ATT+1A10 - ( Type aannemende begunstigde ) -(0..1) - (AcceptingBeneficiaryCode)
84
	
1
	  	
BIN+1911 - ( Begunstigde heeft contractvoordeel aanvaard ) -(0..1) - (BeneficiaryAcceptedTheContractualBenefitBinary)
85
	
1
	  	
MOA+225 - ( Begunstigde - bedrag ) -(0..1) - (BeneficiaryAmount)
86
	
1
	  	
PCD+073 - ( Begunstigde - quotum ) -(0..1) - (BeneficiaryQuotaPercentage)
87
	
1
	  	
FTX+019 - ( Beschrijving ) -(0..1) - (DescriptionText)
88
	
1
	  	
XRT+3 - ( ) -(1..1) - ()
89
	
1
	  	
XRH+3 - ( ) -(1..1) - ()
90
	
1
	  	
PTY+049 - ( Begunstigde bij overlijden ) -(0..n) -(Min. 40% of data expected) - (PartyBeneficiaryInCaseOfDeath)
91
	
1
	  	
NME+001 - ( Officiële naam ) -(0..1) - (OfficialName)
92
	
1
	  	
ADR+002 - ( Adres ) -(0..1) - (OfficialAddress)
93
	
1
	  	
ATT+1910 - ( Type standaardbegunstigde ) -(0..1) - (StandardBeneficiaryCode)
94
	
1
	  	
ATT+1912 - ( Rang ) -(1..1) - (BeneficiaryRankOrderCode)
95
	
1
	  	
ATT+1A10 - ( Type aannemende begunstigde ) -(0..1) - (AcceptingBeneficiaryCode)
96
	
1
	  	
BIN+1911 - ( Begunstigde heeft contractvoordeel aanvaard ) -(0..1) - (BeneficiaryAcceptedTheContractualBenefitBinary)
97
	
1
	  	
MOA+225 - ( Begunstigde - bedrag ) -(0..1) - (BeneficiaryAmount)
98
	
1
	  	
PCD+073 - ( Begunstigde - quotum ) -(0..1) - (BeneficiaryQuotaPercentage)
99
	
1
	  	
FTX+019 - ( Beschrijving ) -(0..1) - (DescriptionText)
100
	
1 


FONDSEN

118
	
1
	  	
DTM+141 - ( Datum inventariswaarde ) -(0..1) - (InventoryValuationDate)
119
	
1
	  	
DTM+142 - ( Datum laatste opvraging ) -(0..1) - (LastWithdrawalDate)
120
	
1
	  	
DTM+143 - ( Datum laatste storting ) -(0..1) - (LastDepositDate)
121
	
1
	  	
DTM+145 - ( Periodiciteit van de storting ) -(0..1) - (DepositsPeriodicityDuration)
122
	
1
	  	
DTM+146 - ( Periodiciteit van de opvraging ) -(0..1) - (WithdrawalsPeriodicityDuration)
123
	
1
	  	
RFF+079 - ( ISIN-code ) -(0..1) - (IsinCodeReference)
124
	
1
	  	
RFF+104 - ( Referte fonds (maatschappij) ) -(0..1) - (FundReferenceCompanyReference)
125
	
1
	  	
RFF+115 - ( Stop-Loss doelfonds ISIN-code ) -(0..1) - (StopLossTargetFundIsinReference)
126
	
1
	  	
RFF+117 - ( Stop-Loss doelfonds Referte (maatschappij) ) -(0..1) - (StopLossTargetFundCompanyReference)
127
	
1
	  	
RFF+116 - ( Profit-Locking doelfonds ISIN-code ) -(0..1) - (ProfitLockingTargetFundIsinReference)
128
	
1
	  	
RFF+118 - ( Profit-Locking doelfonds Referte (maatschappij) ) -(0..1) - (ProfitLockingTargetFundCompanyReference)
129
	
1
	  	
RFF+119 - ( Drip-Feed doelfonds ISIN-code ) -(0..1) - (DripFeedTargetFundIsinReference)
130
	
1
	  	
RFF+120 - ( Drip-Feed doelfonds Referte (maatschappij) ) -(0..1) - (DripFeedTargetFundCompanyReference)
131
	
1
	  	
ATT+A007 - ( Financiële profilering ) -(1..1) - (FinancialProfilingCode)
132
	
1
	  	
ATT+1408 - ( Oorsprong investering ) -(1..1) - (OriginOfInvestmentCode)
133
	
1
	  	
ATT+1411 - ( Stop Loss bescherming ) -(0..1) - (FundStopLossCode)
134
	
1
	  	
ATT+1412 - ( Stop Loss bescherming met herbelegging ) -(0..1) - (FundStopLossReinvestedCode)
135
	
1
	  	
ATT+1413 - ( Profit Locking bescherming ) -(0..1) - (FundProfitLockingCode)
136
	
1
	  	
ATT+1414 - ( Drip-Feed fonds ) -(0..1) - (FundDripFeedCode)
137
	
1
	  	
ATT+1007 - ( Risico label ) -(0..1) - (RiskLabelCode)
138
	
1
	  	
MOA+168 - ( Waarde per eenheid van het fonds bij aankoop ) -(0..1) - (InventoryFundUnitValueAmount)
139
	
1
	  	
MOA+169 - ( Bedrag van de bruto storting ) -(0..1) - (GrossAmountDepositedAmount)
140
	
1
	  	
MOA+170 - ( Bedrag van de netto storting ) -(0..1) - (NetAmountDepositedAmount)
141
	
1
	  	
MOA+171 - ( Bedrag van de geplande storting ) -(0..1) - (AmountPlannedForDepositAmount)
142
	
1
	  	
MOA+172 - ( Bedrag van de geplande opvraging ) -(0..1) - (AmountPlannedForWithdrawalAmount)
143
	
1
	  	
MOA+173 - ( Waarde op de inventarisdatum ) -(0..1) - (ValueAsOnInventoryDateAmount)
144
	
1
	  	
QTY+132 - ( Aantal eenheden van het fonds ) -(0..1) - (NubmerOfUnitsOfTheFundQuantity)
145
	
1
	  	
PCD+049 - ( Percentage instapkosten initiële storting ) -(0..1) - (PercentageBoardingFeeInitialDepositPercentage)
146
	
1
	  	
PCD+050 - ( Percentage instapkosten aanvullende storting ) -(0..1) - (PercentageBoardingFeeAdditionalDepositsPercentage)
147
	
1
	  	
PCD+062 - ( Percentage van de netto investering ) -(0..1) - (TheNetInvestmentPercentage)
148
	
1
	  	
PCD+063 - ( Percentage van de herbelegging ) -(0..1) - (TheReInvestmentPercentage)
149
	
1
	  	
FTX+042 - ( Naam van het fonds ) -(1..1) - (FundNameText)
150
	
1
	  	
FTX+046 - ( URL Algemene Voorwaarden ) -(0..1) - (UrlGeneralClausesText)
151
	
1
	  	
FTX+057 - ( URL Product-fiche ) -(0..1) - (UrlProductDescriptionText)
152
	
1 




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
