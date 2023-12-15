# Semrush
<hr>

### ProjectsJob
Επειδή δεν έχουμε συχνά inserts εδώ, το job είναι inactive.  
Για τον λόγο αυτό υπάρχει στο admin του cdp αντίστοιχη φόρμα για create κάποιου semrush project.
API details:
- Endpoint: https://api.semrush.com/management/v1/projects
- Update all existing projects
- Price: 100 API units per request
- Docs: https://developer.semrush.com/api/v3/projects/projects/#list-all-existing-projects

### ProjectSiteAuditsJob
Τρέχει μια φορά τον μήνα και κάνει update μόνο στον αντίστοιχο πίνακα `semrush_project_site_audits`.  
Για κάθε enabled project θα γίνει API κλήση στο παρακάτω endpoint.
API details:
- Endpoint: https://api.semrush.com/reports/v1/projects/{PROJECT_ID}/siteaudit/info'
- Description: Get overview of the last audit for all projects that belong to site_id
- Price: 100 API units per request
- Docs: https://developer.semrush.com/api/v3/projects/site-audit/#get-information-about-a-campaign

### ProjectPositionTrackingsJob
Επειδή υπάρχει API για να πάρουμε τα position tracking ανά semrush project, ο κύριος πίνακας semrush_project_position_trackings
ενημερώνεται απο τις αντίστοιχες φόρμες στο admin του cdp.  
Μέσω αυτού του job που τρέχει επίσης 1 φορά τον μήνα για κάθε enabled project και κάθε enabled semrush_project_position_tracking
ενημερώνουμε τους:  
`semrush_project_position_tracking_campaigns`  
`semrush_project_position_tracking_positions`, `semrush_project_position_tracking_position_insights`   
`semrush_project_position_tracking_visibilities`, `semrush_project_position_tracking_visibility_insights`.  
Συγκεκριμένα:

Για τον `semrush_project_position_tracking_campaigns`: 
- Endpoint: https://api.semrush.com/reports/reports/v1/projects/{POSITION_TRACKING_CAMPAIGN_ID}/tracking';
- Description: Get the number of keywords that land the specified domain on each of the top 100 positions on SERP
- Price: 100 API units per request
- Docs: https://developer.semrush.com/api/v3/projects/position-tracking/#organic-overview

Για τους `semrush_project_position_tracking_positions` και `semrush_project_position_tracking_position_insights`:
- Endpoint: https://api.semrush.com/reports/reports/v1/projects/{POSITION_TRACKING_CAMPAIGN_ID}/tracking';
- Description: This request lists all keywords from a tracking campaign, Google’s top 100 rankings of the specified URLs for these keywords, and position changes over the selected timeframe.
- Price: Price: 100 API units per keyword added
- Docs: https://developer.semrush.com/api/v3/projects/position-tracking/#organic-positions-report
 
Για τους `semrush_project_position_tracking_visibilities` και `semrush_project_position_tracking_visibility_insights`:
- Endpoint: https://api.semrush.com/reports/reports/v1/projects/{POSITION_TRACKING_CAMPAIGN_ID}/tracking';
- Description: This request returns a domain’s visibility and visibility changes over the selected period.
- Price: 100 API units per request
- Docs: https://developer.semrush.com/api/v3/projects/position-tracking/#organic-visibility-index-report
