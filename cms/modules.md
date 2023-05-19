# Modules
Έχουν Permissions και Access. Εμφανίζονται ως content σε site.

Ένα Module αποτελεί ένα αυτόνομο & αυτοτελές τμήμα ενός site.

## Συνδέσεις (γενικά)
* `1 Module είναι ενός Module Type`. Μέσω του Module Type καθορίζεται πλήρως 
τόσο η λειτουργικότητα όσο και η εμφάνιση ενός Module. 
* Ένα Module μπορεί να συνδεθεί με 1 Module Position. Το Module Position καθορίζει το σημείο του site στο οποίο θα εμφανιστεί ένα Module.
* Ένα Module μπορεί να έχει 0-Ν Module Assignments. Τα Module Assignments καθορίζουν μέσω διάφορων κανόνων σε ποιες σελίδες του site μπορεί να εμφανιστεί
ένα module.
* Ένα Module μπορεί να ανήκει σε 0-N Module Groups. Τα Module Groups δεν επηρεάζουν σε τίποτα το Module. Αποτελούν απλά έναν τρόπο 
φιλτραρίσματος τους στο διαχειριστικό.

## Module Types
Το ModuleType ενός Module καθορίζει σχεδόν τα πάντα για αυτό. Εκτός από τα βασικά-default πεδία έχει και description, version & date (για να έχουμε μια πιο
επίσημη εικόνα για τις αλλαγές που γίνονται, αν και τα πεδία αυτά πρακτικά δεν έχουν χρησιμοποιηθεί ακόμα) και package_id καθώς μπορεί να προέρχεται από κάποιο
πακέτο (περισσότερα στα Πακέτα).

## Module Positions
Περιέχει μόνο βασικά-default πεδία (title, slug κλπ.). Χρησιμοποιείται στο site (views) με τον εξής τρόπο: 
```php
@if(ModuleFacade::moduleExists($modules, 'account'))
    {!! ModuleFacade::getModules($modules, 'account') !!}
@endif
```
, όπου 'account' το slug του ModulePosition των Modules του οποίου που θέλουμε να εμφανίσουμε. Τo "κύριο" ModulePosition ενός Module 
(αυτό που ορίζεται μέσω του module_position_id στον πίνακα modules) μπορεί να γίνει override μέσω των Module Assignments (βλέπε Module Assignments).
Τo `order` των Modules έχει ουσία μόνο για τα Modules του ίδιου ModulePosition (άρα εξαρτάται από το ModulePosition).

## Module Assignments
Τα Module Assignments είναι από 0 έως Ν σετ από κανόνες που καθορίζουν το σε ποιες σελίδες θα εμφανίζεται ένα Module. Μερικοί από τους κανόνες που είναι διαθέσιμοι είναι:
* Επιλογή συγκεκριμένων Menu στα οποία θα εμφανίζεται (ή δε θα εμφανίζεται) το Module
* Είδος σελίδας στο οποίο θα εμφανίζεται (ή όχι) το Module (π.χ. σελίδα Item, Category...)
* Ρόλος χρήστη (guest/god...)
* Ημερομηνίες, ώρες κατά τις οποίες θα εμφανιζεται (ή δε θα εμφανίζεται)

Δεν εμφανίζονται πουθενά παρά μόνο στις φόρμες των Modules, αποκλειστικά στο tab `Assignments`. Στη λειτουργικότητά τους στη φόρμα ακολουθείται σε μεγάλο ποσοστό
η λογική που ακολουθείται στα Tags (WithTags Trait κλπ). Τα αρχεία:
* `app/Traits/WithAssignments.php`
* `resources/views/admin/form/partials/tabs/assignments-tab/*`: όλ α τα views που χρησιμοποιούνται στο tab `Assignments`
* `app/Helpers/Admin/Assignments/ModuleAssignments.php` & `app/Helpers/Admin/Assignments/BaseAssignments.php`: περιέχουν τα πεδία (= τους διαθέσιμους κανόνες).

Στα Modules επίσης μπορεί να συνδεθεί ένα ModulePosition και το αντίστοιχο order με ένα συγκεκριμένο Assignment Set. Έτσι, στην περίπτωση που το Module
εμφανίζεται σε μια σελίδα λόγω του συγκεκριμένου Assignment Set, θα εμφανιστεί στο συγκεκριμένο ModulePosition, με το συγκεκριμένο order. Αν σε ένα Assignment Set δεν έχει
οριστεί ModulePosition/order, το Module θα εμφανιστεί στο default ModulePosition του (με το default order).

Κατά το σερβίρισμα δεδομένων στο Site, ο έλεγχος για τους κανόνες των Assignments ενός Module γίνεται στο `app/Operations/Assignments/ValidateModuleBasedOnAssignmentsOperation.php`.

Η κύρια λειτουργικότητα των Assignments τόσο στο admin όσο και στο site δεν είναι απαραίτητα συνδεδεμένη με τα Modules. Assignments υπάρχουν και για το Popup, ακολουθώντας
περίπου την ίδια λογική (εξαίρεση αποτελεί το override των ModulePosition & order ενός Module). Προφανώς απαιτείται ο σχετικός πίνακας στη βάση και ορισμένα νέα αρχεία. Παραδείγμα για να
λειτουργήσουν τα Assignments στα Popups άλλαξαν τα εξής:
* `module_assignments` πίνακας => νέος πίνακας `popup_assignments` (χωρίς τα πεδία για module_position_id και order)
* `app/Helpers/Admin/Assignments/ModuleAssignments.php` => νέο αρχείο `app/Helpers/Admin/Assignments/PopupAssignments.php`
* `app/Operations/Assignments/ValidateModuleBasedOnAssignmentsOperation.php` => νέο αρχείο `app/Operations/Assignments/ValidatePopupsBasedOnAssignmentsOperation.php`

## Module Groups
Περιέχει μόνο βασικά-default πεδία (enabled, title, slug κλπ.). Έχει παρουσία ως οντότητα μόνο στο Admin. Στα Modules υπάρχει ως custom φίλτρο
και ως πεδίο σε index & φόρμες αντίστοιχα. Η σύνδεση είναι Many-to-Many.

## Routing
Στο admin, το index είναι 1 και το ίδιο ανεξαρτήτως του ModuleType των Modules. Όμως, οι φόρμες μπορεί να είναι ξεχωριστές για κάθε ModuleType, καθώς μπορεί να
χρειαζόμαστε κάποια έξτρα λειτουργικότητα βάσει των πεδίων που προστίθενται στο Module λόγω του ModuleType του. Έτσι, δημιουργούνται για κάθε
ModuleType τα εξής routes για τις φόρμες:
* `${ BASE_URL }/admin/modules/${ MODULE_TYPE_SLUG }/create`
* `${ BASE_URL }/admin/modules/${ MODULE_TYPE_SLUG }/${ $MODULE_ID }/edit`

Τα routes αυτά υπολογίζονται από αυτή τη συνάρτηση: 
```php 
\App\Facades\Admin\AdminBase\AdminBaseFacade::getModuleTypeRoutes(); 
```
, η οποία καλείται από το `routes/admin.php`

## Module Type Config & Fields
Όπως αναφέρθηκε προηγουμένως, κάθε ModuleType ορίζει κάποια δικά του πεδία, τα οποία κληρονομεί το Module. Τα πεδία αυτά ορίζονται στο αρχείο
`app/Helpers/Admin/ModuleTypes/${ MODULE_TYPE_SLUG }Config.php`. Τα πεδία αυτά μαζί με κάποια άλλα (γενικά πεδία για όλα τα Module Types, όπως τίτλος, link κλπ)
που ορίζονται μέσω του `app/Helpers/Admin/ModuleTypes/BaseModuleTypeConfig.php` προστίθενται στα πεδία της φόρμας ενός Module. Οι τιμές τους
αποθηκεύονται στην JSON κολώνα values του Module, με τις τιμές τους να είναι διαθέσιμες προς χρήση στo Operation του κάθε ModuleType, κατά την προετοιμασία του Module
για την εμφάνισή του στο site.

## Config Injections
Ο σκοπός μας είναι τα `${ MODULE_TYPE_SLUG }Config.php` (τουλάχιστον για τα περισσότερα/βασικά ModuleTypes) να παραμένουν ίδια σε όλα τα projects. Όμως, σε κάθε project μπορεί
να προκύπτει η ανάγκη για ορισμένα "δευτερεύοντα" fields, τα οποία π.χ. μπορεί να καθορίζουν κάποιες λεπτομέρειες για τη στυλιστική εμφάνιση του Module στο site. Αυτά τα "προστιθέμενα" fields
θα ορίζονται μέσω των Config Injections. Τα Config Injections θα ορίζονται στα εξής αρχεία:
`app/Project/Admin/ModuleTypes/ConfigInjections/{ $MODULE_TYPE_SLUG }ConfigInjections.php`
Όλα τα πεδία που θα ορίζονται σε αυτά τα αρχεία θα προστίθενται κανονικά στα υπόλοιπα πεδία του Module.
(μελλοντικά ίσως προστεθούν και άλλες δυνατότητες στα Config Injections)

## Module Type Operations
Στα ModuleType Operations γίνεται όλη η προετοιμασία δεδομένων και η οποιαδήποτε παραμετροποίηση χρειάζεται πριν εμφανιστεί ένα Module στο site. Τα αρχεία αυτά ακολουθούν μια συγκεκριμένη δομή:


## Injections
Ακολουθώντας τη λογική των Config Injections, υπάρχει και μια αντίστοιχη επιλογή και για τα Operations. Με τα Operation Injections μπορεί να προστεθεί έξτρα/custom λειτουργικότητα για το εκάστοτε
project, χωρίς να επηρεαστεί το ίδιο το Operation. Τα Operation Injections ορίζονται στα εξής αρχεία:
`app/Project/Site/ModuleTypes/OperationInjections/${ MODULE_TYPE_SLUG }OperationInjections.php`


## Transformations
Tα Transformations, ακολουθώντας τη λογική των Operation Injections, προσθέτουν τη δυνατότητα για έξτρα αλλαγές στα τελικά δεδομένα που θα σερβιριστούν από το Operation στο view του module.

## Views
Όπως αναφέρθηκε προηγουμένως, τα διαθέσιμα view ενός Module καθορίζονται από το ModuleType του. Τα διαθέσιμα views ενός ModuleType ορίζονται στον εξής φάκελο:
`resources/site/modules/${ MODULE_TYPE_SLUG }/views/*` και έχουν *υποχρεωτικά* την εξής μορφή: `{ $VIEW_NAME }/main.blade.php`. Συνήθως, σε όλα τα views θέλουμε να παίρνουμε τις εξής βασικές παραμέτρους
(είναι ανεξάρτητες από το ModuleType του Module):
```php
@php
    $moduleId = data_get($module, 'module.id', 0);
    $mainTitle = data_get($module, 'data.general.title', '');
    $mainSubtitle = data_get($module, 'data.general.subtitle', '');
    $mainText = data_get($module, 'data.general.text', '');
    $mainLinkLabel = data_get($module, 'data.general.link-label', '');
    $mainLink = data_get($module, 'data.general.link', '');
    $mainTarget = data_get($module, 'data.general.target', '_self');
@endphp
```

## Modules & Ajax

## Modules & Caching

## Module & Package

## Module Facade