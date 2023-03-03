## moosend_mailing_lists  
Η σύνδεση των sites με τα mailing lists  
- `id`
- `mailing_list_id` (string) [το id του mailing list από το moosend]
- `site_id` (unsigned big int) [το id από τον πίνακα sites]
- `created_at` (timestamp)
- `updated_at` (timestamp)

## moosend_campaigns
Πίνακας μ’όλα τα campaigns του moosend και τα στατιστικά τους
- `id`
- `api_campaign_id` (string) [το id του campaign από το moosend]
- `name` (string) [το όνομα του campaign]
- `status` (integer)
  το status του campaign με βάση της εξής λίστα: 
  - 0: For Draft.
  - 1: For Queued for sending.
  - 3: For Sent. 
  - 4: For Not enough credits.
  - 5: For Awaiting delivery.
  - 6: For Sending.
  - 10: For Deleted.
  - 11: For Selecting winner.
  - 12: For Archived.
  - 13: For Subscription expired.
  - 14: For Subscription limits exceeded.
- `delivered_on` (timestamp) [πότε παραδόθηκε το campaign]
- `total_sent_emails` (integer) [σε πόσα email στάλθηκε]
- `total_opens` (integer) [πόσες φορές έχει ανοίξει συνολικά το campaign]
- `unique_opens` (integer) [πόσοι χρήστες έχουν ανοίξει το campaign]
- `total_complains` (integer) [πόσα παράπονα υπάρχουν για το συκγκεκριμένο campaign]
- `total_forwards` (integer) [πόσες προωθήσεις έχουν γίνει.]
- `unique_forwards` (integer) [πόσοι προωθήσαν το campaign.]
- `total_unsubscribes` (integer) [πόσοι έκανα unsubscribe μέσα από αυτό το campaign]
- `total_link_clicks` (integer) [πόσες φορές πατήθηκε ένα link του campaign.]
- `unique_link_clicks` (integer) [πόσοι χρήστες πάτησαν οποιδήποτε link του campaign.]
- `total_opens_by_location` (text) [πόσες φορές έχει ανοίξει συνολικά το campaign ανά περιοχή]
- `unique_opens_by_location` (text) [πόσοι χρήστες έχουν ανοίξει το campaign ανά περιοχή]
- `created_at` (timestamp)
- `updated_at` (timestamp)

## moosend_campaign_moosend_maling_list
Σύνδεση moosend mailing list με moosend campaign
- `id`
- `moosend_campaign_id` (unsigned big integer)
- `moosend_mailing_list_id` (unsigned big integer)
- `created_at` (timestamp)
- `updated_at` (timestamp)

## moosend_links
Πίνακας με τα link που υπάρχουν σ’όλα τα campaigns
- `id`
- `link`(string)
- `created_at` (timestamp)
- `updated_at` (timestamp)

## moosend_campaign_moosend_link
Σύνδεση moosend link με moosend campaign
- `id`
- `moosend_link_id`  (unsigned big int)
- `moosend_campaign_id`  (unsigned big int)
- `total_clicks` (integer) [πόσες φορές πατήθηκε το link σ’αυτό το campaign]
- `unique_clicks` (integer) [πόσοι χρήστες έχουν πατήσει το link σ’αυτό το campaign]
- `created_at` (timestamp)
- `updated_at` (timestamp)

## moosend_users
Οι χρήστες του moosend.
- `id`
- `email` (string)
- `name` (string)
- `status` (string) [το status του user με βάση της εξής λίστα: 'Subscribed', 'Unsubscribed', 'Bounced','Removed'.  
  προς το παρόν κρατάμε μόνο όσοι έχουν status Subscribed]
- `moosend_mailing_list_id` (unsigned big integer) [σε ποιο mailing list ανήκει ο χρήστης]
- `visitor_id` (unsigned big integer) [αν για κάποιο χρήστη (με βάση το email) μπορεί να συνδεθεί με κάποιον visitor από τον πίνακα visitors]
- `created_at` (timestamp)
- `updated_at` (timestamp)

## moosend_campaign_moosend_user
Σύνδεση moosend  user με campaign και τα στατιστικά τους
- `id`
- `moosend_campaign_id` (unsigned big integer)
- `moosend_user_id` (unsigned big integer)
- `opened` (boolean) [True ή false αν άνοιξε το συγκεκριμένο campaign]
- `links_clicked` (integer) [πόσα link πάτησε]
- `forward` (boolean) [true ή false αν έκανε προώθηση το συγκεκριμένο campaign]
- `unsubscribed` (timestamp) [την ημερομηνία που έκανε unsubscribe]
- `bounced` (boolean) [true σε περίπτωση που απέτυχε η αποστολή του campaign στον χρήστη]
- `complained_at` (timestamp) [την ημερομηνία που έκανε παράπονο]
- `created_at` (timestamp)
- `updated_at` (timestamp)

## moosend_campaigns_to_sync
Αυτός ο πίνακας δηλώνει πότε ένα campaign είναι έτοιμο για να λάβει πληροφορίες για γεμίσει τον πίνακα moosend_campaign_moosend_user
- `id`
- `moosend_campaign_id`  (unsigned big integer)
- `api_campaign_id` (string)
- `status` (string) [Μπορεί να έχει δύο status ready_to_sync και completed]
- `synced_at` (timestamp) [πότε έγινε sync ή αλλιώς πότε το status έγινε completed]
- `notes` (text)
- `created_at` (timestamp)
- `updated_at` (timestamp)
