## meta_facebook_pages  
Ο πίνακας αυτός αναπαριστά κάποια βασικά metrics σχετικά με το Facebook Page στην περίοδο του χρόνου.  
Συγκεκριμένα τα metrics page_impressions και page_engaged_users μας επιστρέφουν 2 τιμές (σαν end_date_0 και end_date_1) σχετικά με τις δύο προηγούμενες μέρες ως τις 08:00 το πρωι.  
Ο πίνακας ανανεώνεται κάθε μέρα στις 08:30  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `site_id` int(11) [ 1 == 'Golden Hall']
- `page_id` bigint(20) [The unique facebook page account id]
- `fan_count_lifetime` bigint(20) [The total likes of the page at the moment]
- `page_impressions_unique_day` bigint(20) [API: The number of people who had any content from your Page or about your Page enter their screen. This includes posts, stories, check-ins, ads, social information from people who interact with your Page and more.]
- `page_impressions_unique_week` bigint(20) 
- `page_impressions_unique_days_28` bigint(20)
- `page_impressions_day` bigint(20) [API: The number of times any content from your Page or about your Page entered a person's screen.]
- `page_impressions_week` bigint(20) 
- `page_impressions_days_28` bigint(20)
- `page_engaged_users_day` bigint(20) [API: The number of people who engaged with your Page. Engagement includes any click.]
- `page_engaged_users_week` bigint(20)
- `page_engaged_users_days_28` bigint(20) 
- `metrics_end_time` datetime [Αφορά όλα τα metrics τύπου page_impressions και page_engaged_users] 
- `deleted_at` timestamp
- `created_at` timestamp
- `updated_at` timestamp
  
## meta_facebook_posts  
Τραβάει κάθε μέρα στις 08:40 τα 30 τελευταία posts.  
Τα posts γίνονται insert με `sync` = 'PENDING' και παραμένουν έτσι έως ότου το αντίστοιχο job για τα insights το αλλάξει σε 'COMPLETED'.   
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `sync` varchar(191) [Statuses: 'PENDING', 'COMPLETED']
- `meta_facebook_page_id` bigint(20) [FOREIGN KEY 'page_id' of table 'meta_facebook_pages']
- `post_id` varchar(191) [The unique id of the post]
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp  

## meta_facebook_post_insights
Κάθε 5 λεπτά κοιτάει τον πίνακα 'meta_facebook_posts' για κάθε post που έχει `sync` == 'PENDING'.
Μόλις γίνει update ή insert το post στον πίνακα meta_facebook_post_insights τότε αλλάζει και το status στον πίνακα meta_facebook_posts σε 'COMPLETED'.  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `meta_facebook_post_id` varchar(191) [FOREIGN KEY 'post_id' of table 'meta_facebook_posts']
- `reactions` int [Total count of reactions]
- `comments` int [Total count of comments]
- `shares` int [Total count of shares]
- `clicks` int [Total count of clicks]
- `message` text [The message written in the post]
- `post_impressions_paid_lifetime` int [API: The number of times your Page's post entered a person's screen through paid distribution such as an ad.]
- `post_impressions_organic_lifetime` int [API: The number of times your Page's posts entered a person's screen through unpaid distribution.]
- `post_impressions_unique` int [API: The number of people who had your Page's post enter their screen. Posts include statuses, photos, links, videos and more.]
- `post_engaged_users` int [API: The number of people who clicked anywhere in your posts.]
- `created_time` datetime [The post's created time]
- `updated_time` datetime [The post's updated time]
- `permalink_url` varchar(191) [The post's url]
- `deleted_at` timestamp 
- `created_at` timestamp 
- `updated_at` timestamp
