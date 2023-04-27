# Scheduler
```php
$schedule->job(new InstagramAccountsJob($siteSlug = 'ghall'))->dailyAt('08:35');
$schedule->job(new InstagramMediaJob($siteSlug = 'ghall', $mediaLimit = 30 ))->dailyAt('08:45');
$schedule->job(new InstagramMediaInsightsJob($siteSlug = 'ghall', $syncType = 'PENDING'))->twiceDaily(1, 13);
$schedule->job(new InstagramMediaInsightsJob($siteSlug = 'ghall', $syncType = 'TO_BE_COMPLETED'))->weekly();
```

## meta_instagram_accounts / InstagramAccountsJob
Ο πίνακας αυτός αναπαριστά κάποια βασικά metrics σχετικά με το Instagram Account στην περίοδο του χρόνου.   
Συγκεκριμένα :  
-> Τα metrics reach\_day και impressions\_day μας επιστρέφουν 2 τιμές (σαν end\_date\_0 και end\_date\_1) σχετικά με τις δύο προηγούμενες μέρες ως τις 08:00 το πρωι.  Όλα τα υπόλοιπα metrics τύπου \_day γίνονται insert στο datetime του  end\_date\_1 (δηλαδή στο σημερινό date όπου γίνεται η κλήση του API).  
-> Τα metrics τύπου \_lifetime είναι json  
Ο πίνακας ανανεώνεται κάθε μέρα στις 08:35 ενώ γίνονται 3 κλήσεις στο API <account_id>/insights για όλα τα διαφορετικά metrics
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `site_id` int(11) [ 1 == 'Golden Hall']
- `account_id` bigint(20) [The unique instagram account id]
- `followers_lifetime` bigint(20) [The total followers of the account at the moment]
- `daily_metrics_end_time` datetime [Αφορά όλα τα metrics τύπου _day] 
- `reach_day` bigint(20)
- `impressions_day` bigint(20) 
- `website_clicks_day` bigint(20)
- `accounts_engaged_day` bigint(20)
- `total_interactions_day` bigint(20)
- `likes_day` bigint(20) 
- `comments_dayy` bigint(20) 
- `shares_day` bigint(20) 
- `saves_day` bigint(20) 
- `replies_day` bigint(20) 
- `profile_links_taps_day` bigint(20) 
- `audience_gender_age_lifetime` text 
- `audience_locale_lifetime` text
- `audience_country_lifetime` text
- `audience_city_lifetime` text
- `deleted_at` timestamp
- `created_at` timestamp
- `updated_at` timestamp
  
## meta_instagram_media / InstagramMediaJob
Τραβάει κάθε μέρα στις 08:45 τα 30 τελευταία media και τα 30 τελευταία stories μέσω του /media και /stories endpoint αντίστοιχα.
Τα media γίνονται updateOrCreate με `sync` = 'PENDING' και παραμένουν έτσι έως ότου το αντίστοιχο job για τα insights τους αλλάξει το sync status.  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `sync` varchar(191) [Statuses: 'PENDING', 'TO_BE_COMPLETED', 'COMPLETED']
- `meta_instagram_account_id` bigint(20) [FOREIGN KEY 'account_id' of table 'meta_instagram_accounts']
- `media_id` bigint(20) [The unique id of the media]
- `media_type` varchar(191) [Can be 'media' or 'story']
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp  

## meta_instagram_media_insights / InstagramMediaInsightsJob
Δύο φορές την ημέρα (και συγκεκριμένα στις 1:00 & 13:00) κοιτάει τον πίνακα 'meta_instagram_media' για κάθε media/story που έχει   
`sync == 'PENDING'` και θα κάνει updateOrCreate ένα-ένα τα rows του meta_instagram_media_insights μέσω του ../<media_id> endpoint.  
Για κάθε row λοιπόν χρησιμοποιούνται τα 'created_time', 'media_type', 'media_product_type' ως εξής :
- Γίνεται ο έλεγχος στα media_type, media_prododuct_type έτσι ώστε να γίνει update ο πίνακας meta_instagram_TYPE_insight
  - Δηλ. οι πίνακες `meta_instagram_media_reel_insights`, `meta_instagram_media_story_insights`, `meta_instagram_media_carousel_album_insights`, `meta_instagram_media_image_video_insights`.  
Τα endpoinds για αυτούς τους πίνακες δίνουν διαφορετικά δεδομένα.
- Γίνεται update το status στον meta_instagram_media βάση του meta_instagram_media_insight.crated_time: 
  - Αν created_time < SHORT_TERM_EXPIRATION ( = 30 days )  τότε sync = 'PENDING'
  - Αν SHORT_TERM_EXPIRATION < created_time < LIFETIME_EXPIRATION ( = 365 days ) τότε sync = 'TO_BE_COMPLETED'
  - Αν created_time > LIFETIME_EXPIRATION τότε sync = 'COMPLETED' ή αν το media είναι story που τότε το LIFETIME_EXPIRATION = 24 hours. 
 
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `meta_instagram_media_id` bigint(20) [FOREIGN KEY 'media_id' of table 'meta_instagram_media']
- `like_count` int [Total count of reactions]
- `comments_count` int [Total count of comments]
- `media_type` varchar(191) [API: Media type can be CAROUSEL_ALBUM, IMAGE, or VIDEO.]
- `media_type` varchar(191) [API: Media type can be REELS, STORY, FEED or AD.]
- `created_time` datetime [The media's created time]
- `permalink` varchar(191) [The media's url]
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp

## meta_instagram_carousel_album_insights / InstagramMediaInsightsJob
Για media_product_type == 'FEED' &&  media_type == 'CAROUSEL_ALBUM'  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `meta_instagram_media_id` bigint(20) [FOREIGN KEY 'media_id' of table 'meta_instagram_media']
- `carousel_album_engagement` int(11) [API: Total number of likes and IG Comments on the album IG Media object.]
- `carousel_album_impressions` int(11) [API: Total number of times the album IG Media object has been seen.]
- `carousel_album_reach` int(11) [API: Total number of unique Instagram accounts that have seen the album IG Media object.]
- `carousel_album_saved` int(11) [API: Total number of unique Instagram accounts that have saved the album IG Media object.]
- `carousel_album_video_views` int(11) [API: Total number of unique Instagram accounts that have viewed video IG Media within the album.]
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp

## meta_instagram_image_video_insights / InstagramMediaInsightsJob
Για media_product_type == 'FEED' && ( media_type == 'IMAGE' ||  media_type == 'VIDEO' )  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `engagement` int(11) [API: Sum of likes_count, comment_count, and saved counts on the IG Media.]
- `impressions` int(11) [API:  Total number of times the IG Media object has been seen.]
- `reach` int(11) [API:  Total number of unique Instagram accounts that have seen the IG Media object.]
- `saved` int(11) [API:  Total number of unique Instagram accounts that have saved the IG Media object.]
- `video_views` int(11) [API:  Total number of times the video IG Media has been seen. For album IG Media, the number of times all videos within the album have been seen.]
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp

## meta_instagram_reel_insights / InstagramMediaInsightsJob
Για media_product_type == 'REEL'  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `comments` int(11) [API: Number of comments on the reel. Metric in development.]
- `likes` int(11) [API: Number of likes on the reel. Metric in development.]
- `plays` int(11) [API: Number of times the reels starts to play after an impression is already counted. This is defined as video sessions with 1 ms or more of playback and excludes replays. Metric in development.]
- `reach` int(11) [API: Number of unique accounts that have seen the reel at least once. Reach is different from impressions, which can include multiple views of a reel by the same account. Metric is estimated and in development.]
- `saved` int(11) [API: Number of saves of the reel. Metric in development.]
- `shares` int(11) [API: Number of shares of the reel. Metric in development.]
- `total_interactions` int(11) [API: Number of likes, saves, comments, and shares on the reel, minus the number of unlikes, unsaves, and deleted comments. Metric in development.]
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp

## meta_instagram_story_insights / InstagramMediaInsightsJob
Για media_product_type == 'STORY'  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `exits` int(11) [API: Total number of times someone exited the story IG Media object.]
- `impressions` int(11) [API: Total number of times the story IG Media object has been seen.]
- `reach` int(11) [API: Total number of unique Instagram accounts that have seen the story IG Media object.]
- `replies` int(11) [API: Total number of replies (IG Comments) on the story IG Media object. Value does not include replies made by users in some regions. These regions include: Europe starting December 1, 2020 and Japan starting April 14, 2021. If the Story was created by a user in one of these regions, returns a value of 0.]
- `taps_forward` int(11) [API: Total number of taps to see this story IG Media object's next photo or video.]
- `taps_back` int(11) [API: Total number of taps to see this story IG Media object's previous photo or video.]
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp

