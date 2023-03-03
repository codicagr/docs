## meta_instagram_accounts
Ο πίνακας αυτός αναπαριστά κάποια βασικά metrics σχετικά με το Instagram Account στην περίοδο του χρόνου.   
Συγκεκριμένα :  
-> Τα metrics reach\_day και impressions\_day μας επιστρέφουν 2 τιμές (σαν end\_date\_0 και end\_date\_1) σχετικά με τις δύο προηγούμενες μέρες ως τις 08:00 το πρωι.  Όλα τα υπόλοιπα metrics τύπου \_day γίνονται insert στο datetime του  end\_date\_1 (δηλαδή στο σημερινό date όπου γίνεται η κλήση του API).  
-> Τα metrics τύπου \_lifetime είναι json  
Ο πίνακας ανανεώνεται κάθε μέρα στις 08:35.  
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
  
## meta_instagram_media
Τραβάει κάθε μέρα στις 08:45 τα 30 τελευταία media.
Τα posts γίνονται insert με `sync` = 'PENDING' και παραμένουν έτσι έως ότου το αντίστοιχο job για τα insights το αλλάξει σε 'COMPLETED'.   
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `sync` varchar(191) [Statuses: 'PENDING', 'COMPLETED']
- `meta_instagram_account_id` bigint(20) [FOREIGN KEY 'account_id' of table 'meta_instagram_accounts']
- `media_id` bigint(20) [The unique id of the media]
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp  

## meta_instagram_media_insights
Κάθε 5 λεπτά κοιτάει τον πίνακα 'meta_instagram_media' για κάθε media που έχει `sync` == 'PENDING'.
Μόλις γίνει update ή insert το post στον πίνακα meta_instagram_media_insights τότε αλλάζει και το status στον πίνακα meta_instagram_media σε 'COMPLETED'.  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `meta_instagram_media_id` varchar(191) [FOREIGN KEY 'media_id' of table 'meta_instagram_media']
- `like_count` int [Total count of reactions]
- `comments_count` int [Total count of comments]
- `media_type` varchar(191) [API: Media type can be CAROUSEL_ALBUM, IMAGE, or VIDEO.]
- `created_time` datetime [The media's created time]
- `permalink` varchar(191) [The media's url]
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp
