## meta_campaigns
Ο πίνακας με τα διαθέσιμα campaigns ενημερώνεται κάθε μέρα στις 07:00 και τα rows γίνονται update or create ανάλογα με το αν υπάρχουν ή όχι. Τα campaigns γίνονται insert με `` `sync` = 'PENDING' `` και παραμένουν έτσι έως ότου το αντίστοιχο job για τα insights το αλλάξει σε 'COMPLETED'.  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `sync` varchar(191) [Statuses: 'PENDING', 'COMPLETED']
- `name` varchar(191) [API: Campaign's name]
- `campaign_id` bigint(20) [API: Campaign's id]
- `status` varchar(191) [API: A spend cap for the campaign, such that it will not spend more than this cap. Expressed as integer value of the subunit in your currency]
- `created_time` varchar(191) [API: Created Time]
- `start_time` varchar(191) [API: Merging of start_times for the ad sets belonging to this campaign. At the campaign level, start_time is a read only field. You can setup start_time at the ad set level.]
- `stop_time` [API: Merging of stop_times for the ad sets belonging to this campaign, if available. At the campaign level, stop_time is a read only field. You can setup stop_time at the ad set level.]
- `daily_budget` varchar(191) [API: The daily budget of the campaign]
- `lifetime_budget` varchar(191) [API: The lifetime budget of the campaign]
- `spend_cap` varchar(191) [API: The daily budget of the campaign] 
- `deleted_at` timestamp
- `created_at` timestamp
- `updated_at` timestamp

## meta_campaign_insights
Ο πίνακας με τα insights για κάθε campaign.  
Κάθε 5 λεπτά κοιτάει τον πίνακα 'meta_campaigns' και κάνει 3 κλήσεις στο API για κάθε campaign που έχει `` `sync` == 'PENDING' ``, με date_preset => yesterday.
Αυτό γίνεται έτσι ώστε να βλέπουμε την εξέλιξη της καμπάνιας στην πάροδο του χρόνου.
Την 1η φορά γίνεται request στο campaign για τα:  
```php
'fields' => 'social_spend, spend, date_start, date_stop, reach, impressions, clicks, actions, conversions, conversion_values, 
            buying_type, inline_post_engagement, inline_link_clicks, outbound_clicks, frequency, age_targeting, 
            converted_product_quantity, converted_product_value'.
``` 
Την 2η φορά προστίθεται (στο αρχικό request) ``` 'breakdowns' => 'age,gender' ```  
Την 3η φορά προστίθεται (στο αρχικό request) ``` 'action_breakdowns' => 'action_device' ```  
Συνολικά γίνονται insert 3 rows για κάθε campaign_id (ένα ανα περίπτωση)  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `meta_campaign_id` bigint(20) [FOREIGN KEY 'campaign_id' of table 'meta_campaigns']
- `request` text [The request made to the Graph API]
- `response` text [The response from Graph API (nullable)]
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp
