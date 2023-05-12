# Core CDP Documentation
This Documentation is for the "core" CDP entities & processes, that handle the users' actions logging.

# Log Activities
Log Activity is the main data source in the CDP. Each user action (from visiting a page to registering or making a search)
is stored as a single LogActivity record. This record contains all the data around the user action.

### Activity Type:
Log Activities can have one of the following activity types:
1. login
2. logout
3. register
4. visit-page (one per each page load)
5. interaction (eg triggering a filter)
6. search
7. wishlist (adding an item to a user's wishlist)
8. interested in (connecting tags to the user)
9. update-account-details

### LogActivity stored data:
A Log Activity contains the following data:
1. visitor_id
2. activity_type_id
3. site_session_id
4. session_cookie
5. device_id
6. record_type
7. record_id
8. data
9. search_phrase
10. url
11. http_referrer_id
12. first_http_referrer_id
13. http_referer_host
14. utm_id
15. scroll_percentage
16. latest_scroll_at
17. visited_at
18. default timestamps (created_at, updated_at, deleted_at)

CDP receives a request from the site with the necessary data and generates a LogActivity record.

### Steps for generating a LogActivity
In general, the following steps are being followed (see LogActivityController.php for more details):
1. Processing the received request's data (LogActivityController::prepareRequestParameters($request)). Mostly decodes arrays of data.
2. Checking if the site from where the request was received is a valid site registered in the CDP.
3. Finding out the activity-type of the LogActivity.
4. Finding out if the request's intention is to update an existing LogActivity. For now, the only update case is updating scroll percentage
   & latest scroll time for a visit-page request. If the request is an "update-request", the process stops here.
5. Retrieving the visitor (= user) from CDP, based on the request's data. If the visitor does not exist,
   a new visitor record is created.
6. Retrieving the device from CDP, based on the request's data. If the device does not exist, a new device
   record is created.
7. Processing the query string (from the site's url where the request has been sent to the CDP) and getting (or creating)
   the HTTP referrer and the utm of the LogActivity,
8. Getting the related site data of this LogActivity (for example a Product Category)
9. Activity-Type specific steps, such as tags processing, gathering/generating search keywords etc.

### Cookies (visitor_cookie & session_cookie)
1. visitor_cookie: This is the main way to distinguish visitor records. This cookie is a random 32-characters long
2. session_cookie:

### Keywords

# Sites

# Activity Types

# Visitors

# Devices

# Referrers, Campaigns & User Journey
## HTTP Referrers

## Utms

## Campaigns

# Content Entities

## Items & Categories
This is the main content of each connected site. There are methods that sync the CDP's Items & Categories to those from a connected site.

## Tags
Tags are used to express user interest. The majority of the Tags in our system are connected to another Entity record. So, if we have a
store Tag "Adidas" and this Tag gets connected to a user's LogActivity, then we can assume that the user is interested
in the store Adidas & its products.

## Tag Categories
Basically works as a "meta" entity for the Tags. It is used to separate Tags into groups (current Tag Categories: store, brand, shopping-category)
