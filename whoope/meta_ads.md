# Scheduler
```php 
$schedule->job(new CampaignsJob($siteSlug = 'ghall', $campaignLimit = 500))->dailyAt('07:00');
$schedule->job(new CampaignInsightsJob($siteSlug = 'ghall', $campaignLimit = 10))->everyTenMinutes();
```

## meta_campaigns / CampaignsJob
Ο πίνακας με τα διαθέσιμα campaigns ενημερώνεται κάθε μέρα στις 07:00 και τα rows γίνονται update or create ανάλογα με το αν υπάρχουν ή όχι.  
Γίνεται ένα API request στο /campaigns endpoint. Τα campaigns προκειμένουν να γίνουν insert πρέπει να έχουν stop_time είτε μεγαλύτερο της ημερομηνίας που τρέχει το job είτε να είναι null (το null σημαίνει οτι τρέχει χωρίς να λήγει). Για κάθε campaign που γίνεται insert/update φτίαχνονται και τα αντίστοιχα requests που θα γίνουν από το CampaignInsightsJob στο /insights endpoint, τα οποία αποθηκεύονται στον πίνακ meta_campaign_insight_queues.  
Στο 1ο request που φτίαχνεται, έχουμε τα default πεδία για την κλήση του API στο campaign:  
```php
'fields' => 'social_spend, spend, date_start, date_stop, reach, impressions, clicks, actions, conversions, conversion_values, 
            buying_type, inline_post_engagement, inline_link_clicks, outbound_clicks, frequency, age_targeting, 
            converted_product_quantity, converted_product_value'.
``` 
Στο 2ο προστίθεται (στο αρχικό request) ``` 'breakdowns' => 'age,gender' ```  
Στο 3ο προστίθεται (στο αρχικό request) ``` 'action_breakdowns' => 'action_device' ```  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
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

## meta_campaign_insight_queues
Αποθηκεύονται τα requests πριν γίνουν και αφού γίνουν στο ίδιο row. Αν το CampaignInsightsJob εκτελέσει και αποθηκεύσει επιτυχώς το request τότε γεμίζει το completed_at.
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `meta_campaign_id` bigint(20) [FOREIGN KEY 'campaign_id' of table 'meta_campaigns']
- `request` text [The request made to the Graph API]
- `response` text [The response from Graph API]
- `completed_at` timestamp [If null then requested has not been completed]
- `created_at` timestamp
- `updated_at` timestamp

## meta_campaign_insights / CampaignInsightsJob
Ο πίνακας με τα insights για κάθε campaign.  
Κάθε 10 λεπτά κοιτάει τον πίνακα 'meta_campaign_insight_queues' και παίρνει όσα campaign έχουν `` `completed_at` == null `` με limit = 3.    
Αν και το `` `response` == null `` τότε σημαίνει πως το API call δεν έχει γίνει ποτέ και οπότε το εκτελεί (στο /insights endpoint).  
Έχοντας το response, γίνεται updateOrCreate στον πίνακα meta_campaign_insights.  
Ένα response αν έχει breakdowns ή action_breakdowns επιμερίζεται σε πολλαπλά rows στον πίνακα.  
Ενώ για κάθε response αν έχει τιμές τα πεδία `actions`, `conversions` είτε `outbound_clicks` τότε η τιμή τους θα πάει στους αντίστοιχους πίνακες `meta_campaign_insight_actions`, `meta_campaign_insight_conversions`, `meta_campaign_insight_outbound_clicks`.  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `meta_campaign_id` bigint(20) [FOREIGN KEY 'campaign_id' of table 'meta_campaigns']
- `meta_campaign_insight_queue_id` bigint(20) [FOREIGN KEY 'id' of table 'meta_campaign_insight_queues']
- `social_spend`, `spend`, `date_start`,`date_stop`, `reach`, `impressions`, `clicks`, `conversion_values`, `buying_type`, `inline_post_engagement`, `inline_link_clicks`, `outbound_clicks`, `frequency`, `age_targeting`, `converted_product_quantity`, `converted_product_value` <a href="https://developers.facebook.com/docs/marketing-api/reference/ad-campaign-group/insights/" target="_blank">[API Docs]</a>
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp

## meta_campaign_insight_actions
updateOrCreate `value` based on `insights_id`, `action_breakdown`, `action_device`, `action_type`
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `insights_id` bigint(20) [FOREIGN KEY 'id' of table 'meta_campaign_insights']
- `action_breakdown` varchar(191) nullable
- `action_device` varchar(191) nullable
- `action_type` varchar(191) nullable
- `value` int(11)
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp

## meta_campaign_insight_conversions
updateOrCreate `value` based on `insights_id`, `action_breakdown`, `action_device`, `action_type`
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `insights_id` bigint(20) [FOREIGN KEY 'id' of table 'meta_campaign_insights']
- `action_breakdown` varchar(191) nullable
- `action_device` varchar(191) nullable
- `action_type` varchar(191) nullable
- `value` int(11)
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp

## meta_campaign_insight_outbound_clicks
updateOrCreate `value` based on `insights_id`, `action_breakdown`, `name`
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `insights_id` bigint(20) [FOREIGN KEY 'id' of table 'meta_campaign_insights']
- `action_breakdown` varchar(191) nullable
- `name` varchar(191) nullable
- `value` int(11)
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp
