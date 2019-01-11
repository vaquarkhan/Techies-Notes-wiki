http://www.paehl.de/
http://www.paehl.com/open_source/?CURL_7.52.1


**Marketo Example :**



curl --url https://903-eiz-879.mktorest.com/rest/v1/leads.json?access_token=XXXXXXXXXXXXXXXXXXX:sj -X POST -H "Content-Type: application/json" -H "Cache-Control: no-cache" -H "Postman-Token: fc0f0716-1d7b-f655-1f6b-f357617ecee1" -d "{  \"action\":\"createOrUpdate\",   \"lookupField\":\"email\",   \"input\":[        {           \"employeeID\":\"00000001\",         \"employeeCIDNmbr\":\"1234\",         \"lastName\":\"khan\",         \"firstName\":\"viquar\",         \"middleName\":null,         \"busUnitCode\":\"1234\",         \"busUnitName\":\" Finance\",         \"email\":\"Ankur.Nandawat@gmail.com\",         \"employeePosNmbr\":\"sss122\"      }   ]}"   --proxy http://proxy.test.com:8080 --proxy-user test.com/xxx:yyy --proxy-ntlm -k --verbose --negotiate  