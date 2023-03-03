
<pre>
#########################
#### 1 Insert Client ####
#########################

<strong><ins>REQUEST</ins></strong>
Type    : POST
Url     : https://go.s1cloud.net/s1services/set/customer
Headers :
          key  : s1code
          value: YYYYYYYYY
Body    :
{
    "appId": "703",
    "token": "ΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧ",
    "data": {
        "CUSTOMER": [
            {
                "CODE": "1*",
                "ISACTIVE": "1",
                "NAME": "Test Test 2",
                "COUNTRY": "1000|Ελλάς",
                "SOCURRENCY": "100|EURO",
                "BRANCH": "1000|Εδρα",
                "TRDCATEGORY": "1|Λιανικής",
                "PAYMENT": "100|Μετρητοίς"
            }
        ]
    },
    "key": "",                                           "key": "" for new client, "key": "clientID" for client update
    "locateinfo": null
}

<strong><ins>RESPONSE</ins></strong>
{
    "success": true,                    if the Customer inserted or updated (true) otherwise (false)
    "id": 19346,                        the id of Customer ( != with id at go.prosvasis > pelates )
    "readOnly": false,
    "data": {},                         no data because i have set locateinfo to null
    "prtname": "",
    "caption": "10000000000000017292 - Test Test 2",        
    "calc": false,
    "einvoice": false
}

<strong><ins>COMMENTS</ins></strong>
->No problem when inserting a new client with the same CUSTOMER:NAME (new client can be created every time)
</pre>
</br>

<pre>
#########################
###### 2 Get Client #####
#########################

<strong><ins>REQUEST</ins></strong>
Type    : POST
Url     : https://go.s1cloud.net/s1services/get/customer
Headers :
          key  : s1code
          value: YYYYYYYYY
Body    :
{
    "appId": "703",
    "token": "ΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧ",
    "key": "19346",                                ,client id from db -> the id from the previous insert
    "locateinfo": "CUSTOMER:NAME,TRDR"            ,see CASES (optional)
}

<strong><ins>RESPONSE</ins></strong>
{
    "success": true,                              ,if the Customer exists (true) otherwise (false)
    "readOnly": false,
    "data": {                                     ,is the object with the arrays that you request (from locateinfo)
        "CUSTOMER": [
            {
                "NAME": "Test Test 2",
                "TRDR": "19346"
            }
        ]
    },
    "prtname": "",
    "caption": "10000000000000017292 - Test Test 2",
    "calc": false,
    "einvoice": false,
    "remoteKey": ""
}

<strong><ins>CASES</ins></strong> 
1. IF "locateinfo" == "CUSTOMER:NAME" THEN Στο πεδίο data γυρνάει μόνο την επωνυμία
    "data": {
      "CUSTOMER": [ { "NAME": "ALEXANDROS PALIAMPELOS" } ]
    },
2. ELSE IF "locateinfo": "CUSBANKACC:BANK,BANK_BANK_CODE,BANK_BANK_NAME,BANKACNNUM,IBAN,LINENUM;
CUSTOMER:ADDRESS,AFM,CITY,CODE,COUNTRY,DISCOUNT,DISTRICT,EMAIL,GASCUSTYPE,IRSDATA,JOBTYPETRD,NAME,PAYMENT,ZIP"
   THEN Στο πεδίο data γυρνάει τα πεδία που ζητάμε
3. ELSE IF "locateinfo" == null TΗΕΝ data == null
4. Τέλος αν το locateinfo δεν υπάρχει τότε στο πεδίο data περνάει όλες τις πληροφορίες του πελάτη.
</pre>
</br>


<pre>
#########################
####### 3 SAL DOC #######
#########################

<h3> Field description (see examples for required fiels) </h3>

<h4>SALDOC => Bασικά Στοιχεία, Ειδικά Στοιχεία, Διακίνηση Παράδοση</h4>
          Σειρά           => SERIES   => 7068  (ΑΠΥ | Απόδειξη Παραδοχής Υπ.)
          Πελάτης         => TRDR     => 19279 (=> db ID και όχι του εμπορικού)
          Πληρωμή         => PAYMENT  => 100   (=> Μετρητοίς)
          Καθεστώς ΦΠΑ    => VATSTS   => 1
          Υποκατάστημα    => BRANCH   => 1000  (=> έδρα)
          Ημερομηνία      => null (autofill)
          Παραστατικό     => null (autofill)
          Φορολογική Σειρ => null (autofill)
          Φορολογικός Αρ. => null (autofill)
          Ημ/νια εν. απσ. => null (autofill)
          Διάταξη Απαλλαγ => null

<h4>SRVLINES => Γραμμές Τιμολογίου</h4>
          MTRL                => Κωδικός Υπηρεσίας ( Τμήμα Α : 73 | Τμήμα Β: 74 )
          QTY1                => Ποσότητα
          LINEVAL             => Αξία γραμμής
          COMMENTS            => (optional) Σχόλιο γραμμής       
          DISC1PRC            => (optional) Έκπτωση γραμμής
          LINENUM             => (optional) Αριθμός γραμμής
          MTRL_SERVICE_CODE   => (optional) Κωδικός υπηρεσίας
          MTLR_SERVICE_NAME   => (optional) Περιγραφή υπηρεσίας
          PRICE               => (optional) Τιμή μονάδας  | ΑΛΛΑΓΉ ΑΝΤΊΣΤΟΙΧΑ ΑΝ ΥΠΆΡΧΕΙ ΈΠΤΩΣΗ
          VAT                 => (optional) Φ.Π.Α.
          VAT_VAT_PERCNT      => (optional) Ποσοστό Φ.Π.Α.
          MTRUNIT             => (optional) Μονάδα μέτρησης
</br>

#########################
### 3.1 Insert Saldoc ###
#########################

<strong><ins>REQUEST</ins></strong>
Type    : POST
Url     : https://go.s1cloud.net/s1services/set/saldoc
Headers :
          key  : s1code
          value: YYYYYYYYY
Body    :
{
    "appId": "703",
    "token": "ΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧ",
    "data": {
        "SALDOC": [
            {
                "SERIES": "7068",
                "PAYMENT": "100",
                "BRANCH": "1000",
                "TRDR": "19279",
                "VATSTS": "1"
            }
        ],
        "SRVLINES": 
        [
            {
                "MTRL": "73",
                "QTY1": "1",
                "LINEVAL" : "31.06"
            },
            {
                "MTRL": "74",
                "QTY1": "1",
                "LINEVAL" : "8.94"
            }
        ]
    },
    "key": ""
}

<strong><ins>RESPONSE</ins></strong>
{
    "success": true,
    "id": "37061"
}

</br>
<h3> Easy to Retrieve the document once we have the id after insert </h3>
</br>

#########################
#### 3.2 Get Saldoc ####
#########################

<strong><ins>REQUEST</ins></strong>
Type    : POST
Url     : https://go.s1cloud.net/s1services/get/saldoc
Headers :
          key  : s1code
          value: YYYYYYYYY
Body    :
{
    "appId": "703",
    "token": "ΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧΧ",
    "key": "37061",
    "locateinfo" : "SALDOC:FINCODE"
}

<strong><ins>RESPONSE</ins></strong>
{
    "success": true,
    "readOnly": false,
    "data": {
        "SALDOC": [
            {
                "FINCODE": "ΑΠΥ0009255"
            }
        ]
    },
    "prtname": "",
    "caption": "ΑΠΥ0009255 - 16/12/2022",
    "calc": true,
    "einvoice": true,
    "remoteKey": ""
}

</pre>
</br>
