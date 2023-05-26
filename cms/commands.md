# Commands
Τα commands έχουν φτιαχτεί με τρόπο που τα μηνύματα φαίνοντα σωστα σε browser.
Καλύτερα λοιπόν να χρησιμοποιήσουμε το παρακάτω αν θέλουμε να τρέξουμε κάποιο command εκτός του admin περιβάλλοντος.
```php
Route::get('testing/command', function () {
  Artisan::call('command:name');
  dd(Artisan::output());
});
```

## Available Commands
- [Entity (Up/Down) Command](#entity-pseudo-moduletype)
- [PseudoModel (Up/Down) Command](#entity-pseudo-moduletype)
- [ModuleType (Up/Down) Command](#entity-pseudo-moduletype)
- [Explore Packages Command](#explore)
- [Package (Install/Publish/Uninstall) Command](#package)
- [Ecommerce Install Command](#ecommerce)
- [Sitemap Command](#sitemap)

# <a name="entity-pseudo-moduletype">Entity, PseudoModel, ModuleType (Up/Down) Commands</a>
Τα 3 πρώτα commands (6 στο σύνολο με τις ανάποδες διαδικασίες), έχουν την ίδια φιλοσοφία. Να φτιάξω/καταστρέψω ένα Entity, PseudoModel ή ModuleType.
Αυτά τα commands μπορούμε να τα τρέξουμε απο το admin μέσω `CMS Managment > Commands`.  
Στο input πεδίο βάζουμε το όνομα που πρέπει ιδανικά να είναι στον ενικό και με κενό στην μέση.  
π.χ. Sidebar Item και όχι SidebarItem ή SidebarItems.  
Σε περίπτωση που τρέξαμε το command και το όνομα τελικά δεν μας κάνει, το πληκτρολογούμε ξανά με τον ίδιο τρόπο και το διαγράφουμε πατώντας το αντίστοιχο κουμπάκι. 
Σημαντικά εδώ είναι και τα stubs (φάκελος stubs), εκεί βρίσκεται το template του κάθε αντίστοιχου κομμάτι κώδικα που δημιουργείται. Οπότε αν χρειαστεί να αλλαχτεί κάτι στο default template, το αλλάζουμε και από εκεί.  
Πιο συγκεκριμένα,
- Στο Entity (Up/Down) Command μας δίνετε η επιλογή για το αν θα είναι το entity translatable ή οχι και άρα φτάνονται και τα extra πεδία/μοντέλα/migrations. Μόλις εκτελέσουμε το up (create) θα χρειαστεί να τρέξουμε και τα αντίστοιχα migrations που δημιουργούνται. Σημ: τα migrations δεν εκτελούνται γιατί το entity θα αλλάξει σίγουρα.
- Στο PeudoModel (Up/Down) Command δεν έχουμε migrations μιας και είναι models που στηρίζονται πάνω στα category, item models. Άρα μετα την εκτέλεση του up command, είναι έτοιμα προς χρησιμοποίηση/αλλαγή.
- Στο ModuleType (Up/Down) Command έχουμε μόνο τα αντίστοιχα αρχεία που χρειάζονται προκειμένου να στηθεί το module type + τα permissions.  

Σε όλα αυτά οι επιμέρους συναρτήσεις επιστρέφουν void προκειμένου να τρέχουν όλα είτε ολοκληρώθηκε κάτι επιτυχώς είτε όχι.  
Π.χ. Μπορεί τα permissions να υπάρχουν ήδη, δεν θα γίνει break αλλά αντιθέτως θα υπάρχει αντίστοιχο μήνυμα ότι βρέθηκαν τα permissions και άρα δεν χρειάζεται να φτιαχτούν.

# <a name="explore">Explore Packages Command</a>
Αυτό είναι ένα feature που το βρίσκουμε μέσα στο index component του entity Packages.  
Αφού κάνουμε κάποιο πακέτο require μέσω composer τότε θα χρειαστεί να μπει και εκεί προκειμένου να "μπει" στο CMS.  
To composer require απλά θα μας φέρει τον κώδικα στο /vendor, δεν σημαίνει ότι μπορούμε να το χρησιμοποιήσουμε μέσα στην εφαρμογή.  
Να σημειωθεί εδώ πως μιλάμε μόνο για πακέτα που ανήκουν στον vendor codicagr.  
Πατώντας λοιπόν το αντίστοιχο κουμπί στα Packages, το command θα ψάξει για πακέτα που είναι απο codicagr/ και δεν υπάρχουν στο entity Packages.  
Αν βρεθούν τέτοια πακέτα, το command παίρνει το config του πακέτου και δημιουργεί ενα row στα Packages για καθένα από αυτά. Τα πακέτα αυτά μένουν ως disabled μέχρις ότου γίνει κάποιο αντίστοιχο installation.

# <a name="package">Package (Install/Publish/Uninstall) Command</a>
Κάθε πακέτο έχει και τα src/Console/Commands/InstallCommand.php, src/Console/Commands/PublishCommand.php, src/Console/Commands/UninstallCommand.php.  
Αυτές είναι κλάσεις που κάνουν extend τις abstract κλάσεις PackageΙnstallCommand, PackagePublishCommand, PackageUninstallCommand αντίστοιχα.
Οι κλάσεις αυτές είναι abstract γιατί το ``protected $signature`` πρέπει να πηγαίνει ανά πακέτο & ανά command για να λειτουργούν σωστά.
- Install/Uninstall Command: Όπως φαίνεται και παρακάτω τρέχουν παρόμοιες αλλά αντίστροφες διαδικασίες, αξιοποιώντας πάντα το config του πακέτου.  Προκειμένου να τρέξει το command θα χρειαστεί να ικανοποιηθεί ο πρώτος έλεγχος, στη συνέχεια οι λοιπές διαδικασίες δίνουν θα δώσουν το απαραίτητο feedback που χρειάζεται.
```php
//install --> uninstall
if($this->checkRequirements())                    //!$this->isPackageRequired()
{
    $this->publishEssentials(['migrations']);     //$this->removePublished();
    $this->createTables();                        //$this->dropTables();
    $this->updatePackage();                       //$this->disablePackage();
    $this->insertPermissions();                   //$this->removePermissions();
    $this->insertEntities();                      //$this->removeEntities();
    $this->insertSidebarItems();                  //$this->removeSidebarItems();
}
```
- Publish Command: Χρησιμοποιεί το 'publishable' key στο config του πακέτου για να κάνει publish τα αντίστοιχα αρχεία από το vendor στο app.  
Γίνεται αντίστοιχα και η αλλαγή στο ``$package->published_files = ["migrations" => true,``**"config" => true**``];``   
Δεν υπάρχει επιλογή να γίνονται μεμονωμένα αρχεία publish, ότι υπάρχει μέσα στο publishable θα γίνει publish και πλέον το app θα χρησιμοποιεί αυτά τα αρχεία και οχι του package, π.χ App\Helpers\Admin\Entities\PackageEntity και όχι Codicagr\EcPayment\Helpers\Admin\Entities\PackageEntity.    
``$package->published_files =["migrations" => true, "config" => true, 'seeders' => true ]``  
By default, τα migrations είναι publishable μέσω του InstallCommand::publishEssentials().
Η διαφορά με αυτό το function είναι οτι στο publish command έχουμε προαιρετικό publish αρχείων.

# <a name="ecommerce">Ecommerce Install Command</a>
Για να γίνει installation όλων των packages του eCommerce εφόσον υπάρχουν στον vendror, δηλ. εφόσον έχουν γίνει require από composer.  
Με το προαιρετικό option --cleanInstall=true, πρώτα θα γίνουν όλα uninstall και στην συνέχεια install.  
Σε καινούργιο περιβάλλον μπορούμε να τρέξουμε πρώτα το command ExplorePackagesCommand και στη συνέχεια αφού μπουν στα Packages τότε να τρέξουμε αυτό το command για να μπουν όλα με την "σωστή" σειρά μιας και κάποιο πακέτο μπορεί να κάνει require κάποιο άλλο.  

# <a name="sitemap">Sitemap Command</a>
//

