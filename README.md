[![N|Solid](https://jazz.com.pk/themes/jazz/img/logo-desk-new.png)](https://jazz.com.pk)

# Jazz Advance Digital 
Also known as Jazz Red.

## View Loan v2
#### Sample Curl Request
```sh
curl --location 'http://apis.jazz.com.pk/ecare/loan/v2/view' \
--header 'Content-Type: application/json' \
--header 'AuthorizationHeader: Basic SU5UQUNBR0Q6UGF0Y2gjMTIz' \
--header 'Authorization: Bearer <token-here>' \
--data '{
    "ID": "324354223",
    "MSISDN": "923252823178",
    "DAID1": "16",
    "DAID2": "17"
}'
```

#### Sample Response

```sh
{
    "Dedicated_Account_Informations": [
        {
            "Dedicated_Account_ID": "16",
            "Dedicated_Account_Unit": "1",
            "Dedicated_Account_Value": "75.0",
            "Dedicated_Account_ExpiryDate": "99991231T00:00:00+1200"
        },
        {
            "Dedicated_Account_ID": "17",
            "Dedicated_Account_Unit": "1",
            "Dedicated_Account_Value": "30.0",
            "Dedicated_Account_ExpiryDate": "99991231T00:00:00+1200"
        }
    ],
    "Total_Dedicated_Accounts_Value": "105.0",
    "Total_Dedicated_Accounts_InclTax_Value": "120.75"
}
```

> Developed & Maintained by WSO2 Team [ArcanaInfo](https://www.arcanainfo.com/)
