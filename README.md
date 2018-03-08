## Introduction
A simple API built for submitting leads to Anovia Payments.

---
## Authentication
Your Anovia rep will provide you with the following information to access the endpoint. 
- `API_KEY`: Your unique API Key. Pass this in the query string as the value of `code`
- `PARTNER_CODE`: Your partner id. This is a part of your unique URL.
- `CHANNEL_CODE`: The sales channel. Most partners only have one channel, but this field allows partners to submit leads via different sales channels to the same URL. Used in the request body as `channelCode`

Dev Endpoint URL
`https://partner.anoviadev.com/api/v1/{PARTNER_CODE}/lead?code={API_KEY}`
    
Production Endpoint URL
`https://partner.anoviapay.com/api/v1/{PARTNER_CODE}/lead?code={API_KEY}`

---
### API Samples
To send a lead to us, you will need to gather all of the required information listed in the schema below. Once you have all of this, you will need to make a Post request to get all of this information to our endpoint.
Not all fields are needed to compelte the submission process, but the more information we have the better. Refer to the schema for what fields are required for a valid submission. You will notice that some fields have multiple value options. Please ensure that these fields are completed with one of the options provided in the schema. Anything other than what is listed, will result in a 400 Error Response.

This endpoint uses [JSONAPI](http://jsonapi.org/) serialization. Sample cURL Request:

    curl -X POST \
    'https://partner.anoviadev.com/api/v1/{PARTNER_CODE}/lead?code={API_KEY}' \
    {   
        "data": {
            "type": "leads",
            "attributes": {
                "businessName": "Andy's Hardware Store",
                "contact": {
                    "firstName": "Andy",
                    "lastName": "Hanks",
                    "email": "ToyCowboy@pixar.com",
                    "phone": "555-123-1234",
                    "phoneExtension": "0203",
                    "altPhone": "555-123-5678",
                    "altPhoneExtension": "0202",
                    "bestTimeToContact": "10am - 12pm",
                    "timeZone":"CST"
                },
                "businessAddress": {
                    "address1": "123 Main St.",
                    "address2": "Suite 100",
                    "city": "Austin",
                    "state": "TX",
                    "zip": "77777",
                },
                "countryCode": "US",
                "currentAcceptor": true,
                "estimatedMonthlyVolume": "$10,000 - $80,000",
                "paymentAcceptanceMethods": [
                    "Card Present", "eCommerce"
                ],
                "numberOfLocations": 1,
                "productsSold": "Hardware, tools, and more.",
                "channelCode": "{CHANNEL_CODE}",
                "source": "Custom Proposal",
                "externalIdentifier": "{SOME_ID}",
                "submitter": {
                    "externalIdentifier": "{SOME_ID}",
                    "firstName": "Jim",
                    "lastName": "Smith",
                    "email": "jim@smith.com",
                    "phone": "555-123-1234",
                    "relationship": "Friend",
                },
                "notes": "Any additional thoughts, comments, or inquiries from the merchant or agent can be placed here."
            }
        }
    }'
## Successful POST
On a successful submission, you will receive a 200 response code. Any other response means that your lead was not successfully submitted.
## Errors
Any syntactical errors will show:
       
    status: 400
    body: {
        "message": "Invalid Request",
        "errors": [{
            "message": "Request is not in JSONAPI format"
        }]
    }
    
Validation errors will show direct path to issue

        status: 400
        body: {
            "message": "Validation Error",
            "errors": [
            {
            "path": "contact.firstName",
            "message": "Contact First Name is Required"
            },
            {
            "path": "businessAddress.zip",
            "message": "Business Zip can only be 8 characters long."
            },
            {
            "path": "estimatedMonthlyVolume",
            "message": "Estimated Monthly Volume must be one of $0 - $3000, $3000 - $10,000, $10,000 - $80,000, $80,000+"
            }
        ]
    }
### Lead Schema
Name                                    |Type                |Required    |Description
----------------------------------------|--------------------|------------|-------------
businessName                            |string(140)         |True        |The name of the business you are referring
contact.firstName                       |string(140)         |True        |The first name of the decision maker for the business
contact.lastName                        |string(140)         |True        |The last name of the decision maker for the business
contact.email                           |string(140)         |True        |The decision maker's email address
contact.phone                           |string(12)          |True        |The decision maker's phone number
contact.phoneExtension                  |string(8)           |False       |The decision maker's phone number extension
contact.altPhone                        |string(12)          |False       |The decision maker's alternate phone number
contact.altPhoneExtension               |string(8)           |False       |The decision maker's alternate phone number extension
contact.bestTimeToContact               |string(140)         |False       |The best time and/or day to contact the decision maker. <br>Values: <br>8am - 10am<br>10am - 12pm<br>12pm - 2pm<br>2pm - 4pm<br>4pm - 6pm
contact.timeZone                        |string(140)         |False       |The time zone of the business you are referring. <br>Values: <br>PT - Pacific<br>MT - Mountain<br>CT - Central<br>ET - Eastern
businessAddress.address1                |string(140)         |False       |Address of the business you are referring
businessAddress.address2                |string(140)         |False       |Additional address info of the business you are referring
businessAddress.city                    |string(140)         |False       |City of the business you are referring
businessAddress.state                   |string(3)           |False       |State of the business you are reffering
businessAddress.zip                     |string(8)           |False       |Postal code of the business you are referring
countryCode                             |string(140)         |True        |The county of the business you are referring <br>Values: <br>US<br>CA<br>AU
currentAcceptor                         |boolean             |False       |Whether or not your referral currently accepts credit and debit cards
estimatedMonthlyVolume                  |string(140)         |False       |An educated guess of monthly processing volume <br>Values: <br>$0 - $3000<br>$3000 - $10,000<br>$10,000 - $80,000<br>$80,000+
paymentAcceptanceMethods                |string(140)         |False       |Method used to accept card payments. This can have multiples, values are case sensitive and must be listed in an array <br>Values: <br>Card Present<br>eCommerce<br>MOTO<br>Mobile
numberOfLocations                       |number              |False       |Number of locations the business you are referring currently has
productsSold                            |string(140)         |False       |Products or services sold by the business you are referring
channelCode                             |string(140)         |True        |A Channel code will be provided to you by your Anovia liaison
source                                  |string(140)         |False       |Weather or not your merchant would like a custom proposal from us, or if they have already viewed one of our prebuilt programs and are ready to sign up. Values are case sensitive <br>Values: <br>Program<br>Custom Proposal 
externalIdentifier                      |string(36)          |False       |Your organizations unique identifier for your specific leads submitted to Anovia. You can assign your own identifier for your leads, or we will assign one for you on our end.
submitter.externalIdentifier            |string(36)          |False       |Your organizations unique identifier for your agent/employee
submitter.firstName                     |string(140)         |True        |The first name of the agent/employee who submitted the lead
submitter.lastName                      |string(140)         |True        |The last name of the agent/employee who submitted the lead
submitter.email                         |string(140)         |True        |The email address of the agent/employee who submitted the lead
submitter.phone                         |string(12)          |True        |The phone number of the agent/employee who submitted the lead
submitter.relationship                  |string(140)         |False       |The submitters relationship to the decision maker for the business, values are case sensitive <br>Values: <br>Self<br>Friend<br>Family<br>Business Associate<br>Customer<br>Other
notes                                   |string(140)         |False       |Any additional notes or comments from submitter or decision maker
## Support
For questions/concerns please contact <developersupport@anoviapayments.com>
