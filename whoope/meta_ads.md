# Scheduler
```php 
$schedule->job(new CampaignsJob($siteSlug = 'ghall', $campaignLimit = 500))->dailyAt('07:00');
$schedule->job(new CampaignInsightsJob($siteSlug = 'ghall', $campaignLimit = 10))->everyThirtyMinutes();
```

## meta_campaigns
Ο πίνακας με τα διαθέσιμα campaigns ενημερώνεται κάθε μέρα στις 07:00 και τα rows γίνονται update or create ανάλογα με το αν υπάρχουν ή όχι.  
Τα campaigns γίνονται insert με `` `sync` = 'PENDING' `` και παραμένουν έτσι έως ότου το αντίστοιχο job για τα insights το αλλάξει σε 'COMPLETED'.  
Τα campaigns προκειμένουν να γίνουν insert πρέπει να έχουν stop_time μεγαλύτερο ή ίσο της ημερομηνίας που τρέχει το job.  
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
Ο πίνακας με τα insights για κάθε campaign. Στόχος είναι να γίνουν 3 API calls για δοθείσα ημερομηνία.  
Κάθε 30 λεπτά κοιτάει τον πίνακα 'meta_campaigns' και παίρνει όσα campaign έχουν `` `sync` == 'PENDING' `` με limit = 10.**    
Για να έχουμε την εξέλιξη της κάθε καμπάνιας στην πάροδο του χρόνο ορίζεται μια ημερομηνία έναρξης. Αυτή μπορεί να είναι:  
- Είτε η  ``Carbon::now()->subMonths(6)``, δηλ. 6 μήνες πίσω απο το τώρα, 
- είτε η ``MetaCampaignInsight::where('meta_campaign_id', $campaign->campaign_id)->orderBy('date_start', 'desc')->first()``, δηλ. η ημερομηνία για την οποία έχουμε αποθηκευέμνο το νεότερο api call.
Σαν ημερομηνία έναρξης επιλέγεται ότι είναι νεότερο απο τις δύο. 
Στο time frame που έχει τώρα οριστεί, γίνονται 3 API calls/ημερομηνία ξεκινώντας είτε από την ημερομηνία έναρξης έως την χθεσινή ημερομηνία.  

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
