## adwords_campaign_performance_reports
Ο πίνακας campaigns ενημερώνεται κάθε μέρα στις 09:00 και αφορά results χθεσινή ημερομηνία.  
Τα rows γίνονται update ή create βάση του primary key  
``matomo_adwords_campaign_performance_report_pkey`` =>  ['campaign_id', 'site_id', 'reference_date', 'device']  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `campaign_id` bigint(20) [refers to: campaign.id]
- `campaign_name` varchar(191) [refers to: campaign.name]
- `amount` int(11) [refers to: campaign_budget.amount_micros]
- `campaign_status` varchar(100) [refers to: campaign.status]
- `impressions` int(11) [refers to: metrics.impressions]
- `interactions` int(11) [refers to: metrics.interactions]
- `interaction_rate` double [refers to: metrics.interaction_rate]
- `cost` int(11) [refers to: metrics.cost_micros]
- `conversions` double [refers to: metrics.conversions]
- `bidding_strategy_type` varchar(100) [refers to: campaign.bidding_strategy_type]
- `reference_date` date [refers to: segments.date]
- `site_id` int(11) [FK: 1 == 'Golden Hall']
- `account_name` varchar(191) [refers to: customer.descriptive_name]
- `advertising_channel_type` varchar(100) [refers to: campaign.advertising_channel_type]
- `advertising_channel_subtype` varchar(100) [refers to: campaign.advertising_channel_sub_type]
- `content_budget_lost_impression_share` double [refers to: metrics.content_budget_lost_impression_share]
- `device` varchar(100) [refers to: segments.device]
- `created_at` timestamp
- `updated_at` timestamp  

## adwords_adgroup_performance_reports
Ο πίνακας adgroup ενημερώνεται κάθε μέρα στις 09:15 και αφορά results χθεσινή ημερομηνία.  
Τα rows γίνονται update ή create βάση του primary key  
``matomo_adwords_adgroup_performance_report_pkey`` => ['adgroup_id', 'campaign_id', 'site_id', 'reference_date', 'device']  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `adgroup_id` bigint(20) [refers to: adgroup.id]
- `adgroup_name` varchar(191) [refers to: adgroup.name]
- `campaign_id` bigint(20) [refers to: campaign.id]
- `campaign_name` varchar(191) [refers to: campaign.name]
- `campaign_status` varchar(100) [refers to: campaign.status]
- `cpc_bid` int(11) [refers to: ad_group.cpc_bid_micros]
- `target_cpa` int(11) [refers to: ad_group.effective_target_cpa_micros]
- `adgroup_type` varchar(100) [refers to: ad_group.type]
- `adgroup_final_url_suffix` varchar(191) [refers to: ad_group.final_url_suffix]
- `impressions` int(11) [refers to: metrics.impressions]
- `interactions` int(11) [refers to: metrics.interactions]
- `interaction_rate` double [refers to: metrics.interaction_rate]
- `cost` int(11) [refers to: metrics.cost_micros]
- `conversions` double [refers to: metrics.conversions]
- `reference_date` date [refers to: segments.date]
- `site_id` int(11) [FK: 1 == 'Golden Hall']
- `content_impression_share` double [refers to: metrics.content_impression_share]
- `content_rank_lost_impression_share` do$ble [refers to: metrics.content_rank_lost_impression_share]
- `cpv_bid` int(11) [refers to: ad_group.cpv_bid_micros]
- `target_cpa_bid_source` varchar(100) [refers to: ad_group.effective_target_cpa_source]
- `bidding_strategy_id` int(11) [refers to: bidding_strategy.id]
- `bidding_strategy_name` varchar(191) [refers to: bidding_strategy.name]
- `bidding_strategy_type` varchar(100) [refers to: campaign.bidding_strategy_type]
- `view_through_conversions` int(11) [refers to: metrics.view_through_conversions]
- `average_cpm` double [refers to: metrics.average_cpm]
- `device` varchar(100) [refers to: segments.device]
- `created_at` timestamp
- `updated_at` timestamp

## adwords_keyword_performance_reports
Ο πίνακας keyword ενημερώνεται κάθε μέρα στις 09:30 και αφορά results χθεσινή ημερομηνία.  
Τα rows γίνονται update ή create βάση του primary key  
``matomo_adwords_keyword_performance_report_pkey`` => ['adgroup_id', 'campaign_id', 'site_id', 'reference_date', 'device']  
Table columns:  
- `id` bigint(20) [PRIMARY KEY]
- `adgroup_id` bigint(20) [refers to: adgroup.id]
- `adgroup_name` varchar(191) [refers to: adgroup.name]
- `criteria` varchar(191) [refers to: ad_group_criterion.keyword.text]
- `campaign_id` bigint(20) [refers to: campaign.id]
- `campaign_name` varchar(191) [refers to: campaign.name]
- `campaign_status` varchar(100) [refers to: campaign.status]
- `cpc_bid` int(11) [refers to: ad_group_criterion.effective_cpc_bid_micros]
- `impressions` int(11) [refers to: metrics.impressions]
- `interactions` int(11) [refers to: metrics.interactions]
- `interaction_rate` double [refers to: metrics.interaction_rate]
- `cost` int(11) [refers to: metrics.cost_micros]
- `conversions` double [refers to: metrics.conversions]
- `reference_date` date [refers to: segments.date]
- `site_id` int(11) [FK: 1 == 'Golden Hall']
- `active_view_impressions` int(11) [refers to: metrics.active_view_impressions]
- `active_view_measurable_impressions` int(11) [refers to: metrics.active_view_measurable_impressions]
- `active_view_ctr` double [refers to: metrics.active_view_ctr]
- `clicks` int(11) [refers to: metrics.clicks]
- `conversions_from_interactions_rate` double [refers to: metrics.conversions_from_interactions_rate]
- `conversions_value` double [refers to: metrics.conversions_value]
- `search_impression_share` double [refers to: metrics.search_impression_share]
- `search_budget_lost_top_impression_share` double [refers to: metrics.search_budget_lost_top_impression_share]
- `search_rank_lost_top_impression_share` double [refers to: metrics.search_rank_lost_top_impression_share]
- `search_rank_lost_impression_share` double [refers to: metrics.search_rank_lost_impression_share]
- `search_top_impression_share` double [refers to: metrics.search_top_impression_share]
- `bidding_strategy_type` varchar(100) [refers to: campaign.bidding_strategy_type]
- `post_click_quality_score` varchar(100) [refers to: ad_group_criterion.quality_info.post_click_quality_score]
- `quality_score` int(11) [refers to: ad_group_criterion.quality_info.quality_score]
- `creative_quality_score` varchar(100) [refers to: ad_group_criterion.quality_info.creative_quality_score]
- `device` varchar(100) [refers to: segments.device]
- `created_at` timestamp
- `updated_at` timestamp
