# What is Digital COVID-19 Vaccine Record (DCVR)?

In June 2021, the California Department of Technology (CDT) launched the Digital COVID-19 Vaccine Record (DCVR) Portal, implementing the SMART Health Card framework. A Digital Vaccine Record is an electronic vaccination record drawn from the data stored in the California immunization registry. The digital record shows the same information as a resident's paper CDC vaccine card: name, date of birth, vaccination dates, and type of vaccine received. The digital record also includes a QR code that, when scanned by a SMART Health Card reader, will display to the reader your name, date of birth, vaccine dates, and vaccine type. The QR code also confirms the vaccine record as an official record of the state of California. 

# Application Architecture

California's implementation of the application is a three-tier, logically and physically separated architecture: 
1.	A web application using the React JavaScript Library, 
2.	A middle tier written in Microsoft .NET Core with a Node.js API, and 
3.	A backend data tier using Snowflake. 

# Twilio SMS and SendGrid API 
- Once the API determines that information submitted matches an existing record in the immunization registry, a 'found' message is sent via Twilio (SMS) or SendGrid (email). 
- If a match is not found, a failure message is sent via Twilio (SMS) or SendGrid (email). 

# Code Repos
There are a total of three code repositories:
1.	DigitalCovid19VaccineRecord-UI
2.	DigitalCovid19VaccineRecord-API
3.	DigitalCovid19VaccineRecord-QR

Please view the readme file in each repo for further details.


# How to run locally
- Deploy UI Code into a local directory.
- Deploy API Code into a local directory.
- Replace any backend return data set with a JSON data set specified in API readme file.
- Use this example string for recipID = "12345" to generate a sample QR code.
- Use expected JSON structure from database:

```
{ "vc": { "credentialSubject": { "fhirBundle": { "entry": [ { "fullUrl": "resource:0", "resource": { "birthDate": "1951-08-15", "name": [ { "family": "Doe", "given": [ "John" ] } ], "resourceType": "Patient" } }, { "fullUrl": "resource:1", "resource": { "occurrenceDateTime": "2021-02-23", "patient": { "reference": "resource:0" }, "performer": [ { "actor": { "display": "KAISER PERMANENTE NCAL" } } ], "resourceType": "Immunization", "status": "completed", "vaccineCode": { "coding": [ { "code": "208", "system": "http://hl7.org/fhir/sid/cvx" } ] } } }, { "fullUrl": "resource:2", "resource": { "lotNumber": "EN6207", "occurrenceDateTime": "2021-03-16", "patient": { "reference": "resource:0" }, "performer": [ { "actor": { "display": "KAISER PERMANENTE NCAL" } } ], "resourceType": "Immunization", "status": "completed", "vaccineCode": { "coding": [ { "code": "208", "system": "http://hl7.org/fhir/sid/cvx" } ] } } } ], "resourceType": "Bundle", "type": "collection" }, "fhirVersion": "4.0.1" }, "type": [ "https://smarthealth.cards#health-card", "https://smarthealth.cards#immunization", "https://smarthealth.cards#covid19" ] } }
```

# Functionality Overview 
The DCVR is most useful as an official, digital copy of an individual's CDC card. It is an official document issued by the State of California and is sufficient as proof of a vaccination record if a person cannot produce their CDC card.  

Users who scan the QR code will see a long string of text, starting with the prefix "shc:/", which indicates that the information to follow is a SMART Health Card. (iPhone users who try scanning this with their camera get the "no usable data found" message because as of this writing, there is no application on the iPhone associated with the "shc:" prefix.) The long string of text is known as an "FHIR bundle" - FHIR stands for Fast Health Interoperability Resources and is an emerging collection of standards designed to improve healthcare data interoperability. More information is at fhir.org. 

The State of California cryptographically signs this bundle; any reader can validate that signature by using the state's public key, published on the My Vaccine Record page at https://myvaccinerecord.cdph.ca.gov/creds/.well-known/jwks.json . The QR code includes the same information presented on the digital vaccine record webpage: your name, date of birth, and the date(s) of your vaccination(s). When a SMART Health Card-compatible reader scans the QR code, it gives the reader the ability to verify the signature, confirming the authenticity of underlying information. (If the FHIR bundle had been modified, the signature verification would fail, resulting in an unsuccessful scan.)

In this way, a reader can know for certain that an individual with the name presented, born on the day indicated, actually received the vaccination(s) mentioned in the QR code. Those vaccination(s) are registered in the California Immunization Registry. 

It's worth mentioning what the SMART Health Card is not: it's not proof of identity, it is a confirmation of a vaccination record belonging to the individual named, born on the date specified. Any venue or organization that wants to rely on the SMART Health Card as proof of vaccination should match the information contained on an identity document (driver's license, for example). 

A final note on the digital vaccine record system design and ongoing improvements to the system. In order to generate a digital vaccine record, we need to match the contact information you provide with contact information already on file with the California Immunization Registry. In most cases, the vaccination provider submitted the resident's contact information with the report of their vaccination. In those cases, the resident will successfully retrieve their digital vaccine record 100% of the time. However, if your contact information is missing from the immunization registry, we cannot match your information with the vaccination record, and therefore cannot send you a link. We worked hard before launch to ensure that we had as much contact information in the system as possible and have made great strides since we launched. By working closely with the vaccination providers, we've added even more contact information for several million California residents; as a result, we have improved the match rate on digital vaccine record requests. As we get more reports from residents who remain unable to retrieve their digital vaccine records, we are in close contact with each provider so that we can continue to make progress. In the meantime, any resident who wants to can reach out to their provider directly and ask that they send updated contact information to the state's immunization registry or can reach out to CDPH and upload a copy of their CDC card so that the state can update their records. (Submitted information will need to be verified and may take a bit of time before it's reflected in the system.)

---

# Introduction
Function app to generate QR Code of the shc

# Recommended Software & Tools
-	Visual Studio Code
-	Web Browsers (Chrome, Edge, Firefox)

# Installation and Setup
-	Clone the GitHub repository via command line
-	git clone https://github.com/StateOfCalifornia/DigitalVaccineRecord-QR.git 

# How to run from localhost
1.	Clone the repository
2.	Use Visual Studio Code with Azure Functions extension
3.	Run > Start Debugging
Success!
Make note of the URL for API secrets.
Example: `http://localhost:7071/api/QRCreate` 
This value will be used in the API, under the secrets.json file property `{ "AppSettings:QrCodeApi": "url here"}`

#Functions
QRCreate: Generates a QR code of the shc string
