# Cloudsim

!["Cloudsim image"](img/cloudsim.png)

Το Cloudsim είναι ένα framework προσομοίωσης, γραμμένο σε Java, το οποίο επιτρέπει στον χρήστη να ελέγχει κάθε οπτική ενός Cloud περιβάλλοντος (είτε τον αλγόριθμό του, ή την πλατφόρμα του, ή την αρχιτεκτονική του). Με το Cloudsim, ο χρήστης μπορεί να επιβεβαιώσει στρατηγικές στην υπολογιστική νέφους, να προσομοιώσει ένα δικό του cloud περιβάλλον και να χτίσει δικές του λύσεις. Εφόσον είναι προσομοιωτής, δεν μπορεί να τρέξει πραγματική εφαρμογή, να τρέξει μόνο του ή να αντικαταστήσει ένα cloud περιβάλλον.

```sh
#!/bin/bash
java -classpath jars/cloudsim-4.0.jar:jars/cloudsim-examples-4.0.jar org.cloudbus.cloudsim.examples.CloudSimExample${1}
```

## Ανάλυση CloudExample6

Στο CloudExample6, δημιουργούνται 2 datacenters με 2 hosts το καθένα. Μέσα στη main φτιάχνει τα δύο datacenters, ενώ μέσα στη μέθοδο createDatacenter παρατηρείτε έχει δύο hosts το καθένα. Για το κάθε datacenter, οι hosts έχουν μερικά χαρακτηριστικά. Ο host 0, είτε αυτός είναι μέσα στο πρώτο datacenter ή στο δεύτερο, έχει 4 επεξεργαστές, ενώ ο host 1 έχει 2, ενώ μοιράζονται τα υπόλοιπα χαρακτηριστικά τους. Δηλαδή έχουν 2048 MB κύριας μνήμης (RAM), 10 MB δευτερεύουσας μνήμης (storage), 10 KB εύρος ζώνης (bandwidth) και χρησιμοποιούν έναν συγχρονιστή χρόνου που επιτρέπει το host να έχει παραπάνω από ένα επεξεργαστή.
Αφού φτιάξει τα παραπάνω, μπορεί να παρατηρηθεί ότι χρησιμοποιώντας την κλάση DatacenterCharacteristics, έχουν τα παραπάνω χαρακτηριστικά για τα datacenters. Αναλυτικά, ότι είναι x86 αρχιτεκτονικής, ότι τρέχει Linux, ότι ο παρακολουθητής των εικονικών μηχανών (virtual machine monitor) είναι ο xen, ότι βρίσκεται στο δέκατο timezone, το κόστος χρησιμοποιώντας αυτή την υπηρεσία είναι 3, η μνήμη 0.05 και της μνήμης 0.1.
Στη συνέχεια, επιστρέφοντας στην main του προγράμματος, δημιουργεί έναν broker, ο οποίος είναι υπεύθυνος για την διαχείριση των VM. Επιπρόσθετα, αρχίζει να φτιάχνει τις εικονικές μηχανές (**Virtual Machines**, VM), 20 στο σύνολο. Κάθε VM έχει κάποια συγκεκριμένα χαρακτηριστικά. Έχει ένα μέγεθος των 100 KB, ενώ ζητάει να του παραχωρηθεί 512 MB κύριας μνήμης για να μπορεί να τρέξει. Επίσης μπορεί να εξυπηρέτηση μέχρι 1000 εντολές το δευτερόλεπτα (mips), το εύρος ζώνης είναι επίσης 1000 και έχει έναν επεξεργαστή, με τον monitor να είναι ο Xen. Ο αλγόριθμος που χρησιμοποιείται για να γίνει η προσπάθεια ανάθεσης σε κάποιον host είναι ο CloudletSchedulerTimeShared, οπού προσδιορίζει πόσο χρόνο θα έχει το κάθε cloudlet για να χρησιμοποιήσει τους πόρους. Το cloudlet είναι μία βελτιωμένη έκδοση του σημερινού cloud συστήματος. Είναι υπεύθυνο για την ανάθεση των VMs στα host.
Τελικά θα φτιαχτούν 6 VMs για το Datacenter 1 και άλλα 6 για το αλλό. Αυτό επειδή ο host 0 του Datacenter 1 έχει τέσσερις πυρήνες και ο host 1 δύο, όποτε 6 πυρήνες στο σύνολο και το κάθε VM ζητάει έναν τουλάχιστον πυρήνα. Επίσης, βλέπουμε ότι κάθε Host χωράει το μέγιστο 4 VMs, αφού το κάθε VM διαχειρίζεται 512 ΜB ram, δηλαδή το 1/4 της συνολικής ram του host (2048 MB). Ακολουθεί πίνακας για τον αριθμό VM ανά host.

| Virtual Machine | Host | Datacenter |
| --------------- | ---- | ---------- |
| 0, 1, 2, 4 | 0 | 1 |
| 3, 5 | 1 | 1 |
| 6, 7, 8, 10 | 0 | 2 |
| 9, 10 | 1 | 2 |

Μετά τη δημιουργία των VM, αρχίζει να δημιουργεί τα cloudlets, 40 στο σύνολο. Τα χαρακτηριστικά τους είναι: μέγεθος 1000, μέγεθος συστήματος 300, μέγεθος ouput 300 και μονοπύρηνο. Τέλος, χρησιμοποιεί το UtilizationModel για να μπορεί να παρέχει έναν καλό έλεγχο των πόρων και δίνει ιδιοκτηκότητα στον broker.
Για την αντιστοίχιση cloudlet/vm, παρατηρούμε όταν ο broker στέλνει ένα-ένα cloudlet σε όλα τα VM. Αφού έχουμε 40 cloudlet να αντιστοιχήσουμε και 12 VM, σημαίνει ότι το κάθε VM θα έχει 3 cloudlet, με τέσσερα περισσευούμενα, τα οποία θα τα αντιστοιχήσει στα πρώτα 4 VM.
Τέλος, ας τελικά αποτελέσματα. Καταρχάς, παρατηρούμε ότι οι στήλες μας δίνουν τις εξής πληροφορίες: το ID του Cloudlet, το Status του, το ID του Datacenter που ανήκει το VM που φιλοξενεί το Cloudlet, το ID του VM, ο χρόνος που πήρε για να τρέξει το simulation, η αρχική ώρα και η τελική ώρα. Καταρχάς παρατηρούμε ότι όλα τέλειωσαν με τη σειρά που μπήκαν. Κάθε cloudlet πήρε 1 δευτερόλεπτο να τρέξει.

## Εναλλακτικές δυνατότηες

Εφόσον εκτελέστηκε αρχικά με time-sharing, θα τροποποιηθεί ο κώδικας κατάλληλα για να τρέχει με space sharing. Έγιναν οι εξής αλλαγές:

```java
hostList.add(
         new Host(
            hostId,
            new RamProvisionerSimple(ram),
            new BwProvisionerSimple(bw),
            storage,
            peList1,
            new VmSchedulerSpaceShared(peList1)
         )
      ); // This is our first machine

hostId++;

hostList.add(
         new Host(
            hostId,
            new RamProvisionerSimple(ram),
            new BwProvisionerSimple(bw),
            storage,
            peList2,
            new VmSchedulerSpaceShared(peList2)
         )
      ); // Second machine
vm[i] = new Vm(i, userId, mips, pesNumber, ram, bw, size, vmm, new CloudletSchedulerSpaceShared());
```

Το πρώτο πράγμα που μπορεί να παρατηρηθεί είναι ότι παρόλο που έχουμε αλλάξει την στρατηγική πίσω από την δρομολόγηση των VM στους hosts, δεν άλλαξε. Δεν έχει σημασία ο χώρος επειδή είναι υπεραρκετός. Για την δρομολόγηση των Cloudlets στα VM όμως είναι άλλη ιστορία. Παρατηρούμε ότι πρώτα γεμίζουν όπως μπαίνουν. Δηλαδή ο αλγόριθμος είναι FIFO (First in First out), ενώ στο προηγούμενο δεν είχε σημασία το πως έμπαιναν στη λίστα αναμονής, έβγαιναν μόνο όποτε <<γέμιζε>> ένα vm. Με άλλα λόγια όποτε φόρτωνε όλα τα Cloudlets.

## Ελάχιστες απαιτούμενες αλλαγές για να τρέχουν όλα τα VM

Επόμενο βήμα είναι το simulator να τρέχει όλα τα VM. Υπάρχουν δύο τρόποι που θα μπορούσε να τροποποιήθει: είτε να δώθει παραπάνω ram στον host, είτε να αυξήθουν τα cores των host. Αρχικά ας αυξηθεί η ram. Προς το παρόν, οι host έχουν 2048 ΜΒ. Εφτά VM δεν μπορούν να τρέξουν προς το παρόν. Χρειαζόμαστε 3584 MB παραπάνω ram συνολικά για να τρέξουν. Πρέπει να προσθέσουμε τουλάχιστον 1792 MB ram ανά host, φέρνοντας την τελική ram ανά host στα 3840 MB ram. Επιπρόσθετα, παρατηρούμε ότι κάθε CPU μπορεί να δεχθεί 1000 instructions το δευτερόλεπτο, όσες και το κάθε VM. Μαθηματικά, βλέπουμε ότι μας περισσεύουν 6 VM, δηλαδή 6000 MIPS. Εφόσον αυτό ισχύει, θα αυξήσουμε και τα MIPS στα 2000 σε 4 τυχαίους CPUs, για να μπορεί ο κάθε επεξεργαστής να επεξεργαστεί παραπάνω VMs. Αυτό έχει ως αποτέλεσμα να μπορεί να μας εξυπηρετήσει τα περισσευούμενα VM που δε μπορούσε πριν.
Βεβαία, πρώτα θα φορτώσει τα πρώτα 12 όπως και πριν, και με το που αδειάσει κάποια θέση, θα μπει στην άδεια θέση του το νέο VM. Για να λυθεί αυτό το πρόβλημα θα αυξήσουμε τον αριθμό των cores. Θα βάλουμε 2 CPUs παραπάνω ανά host. Βλέπουμε ότι τώρα τρέχουν όλα τα VM.

```java
List<Pe> peList1 = new ArrayList<Pe>();

int mips1 = 2000;
int mips2 = 1000;

// 3. Create PEs and add these into the list.
//for a quad-core machine, a list of 4 PEs is required:
peList1.add(new Pe(0, new PeProvisionerSimple(mips1))); 
// need to store Pe id and MIPS Rating
peList1.add(new Pe(1, new PeProvisionerSimple(mips1)));
peList1.add(new Pe(2, new PeProvisionerSimple(mips1)));
peList1.add(new Pe(3, new PeProvisionerSimple(mips2)));
peList1.add(new Pe(4, new PeProvisionerSimple(mips1)));
peList1.add(new Pe(5, new PeProvisionerSimple(mips1)));

//Another list, for a dual-core machine
List<Pe> peList2 = new ArrayList<Pe>();

peList2.add(new Pe(0, new PeProvisionerSimple(mips1)));
peList2.add(new Pe(1, new PeProvisionerSimple(mips2)));
peList2.add(new Pe(2, new PeProvisionerSimple(mips2)));
peList2.add(new Pe(3, new PeProvisionerSimple(mips2)));
```

Στα αποτελέσματα, αρχικά χωρίς τους έξτρα επεξεργαστές, δε παρατηρήτε μεγάλη διαφορά στην εκτέλεση. Ακόμα, κάθε VM έχει διάρκεια 1 δευτερόλεπτο. Παρομοίως και στους έξτρα επεξεργαστές. Η διαφορά, βέβαια, είναι στο τελικό χρόνο. Αντί να τελειώσει στα 3.2 δευτερόλεπτα, είναι 1 δευτερόλεπτο πιο γρήγορο και τελειώνει στα 2.2 δευτερόλεπτα.

## Αλλαγή πολιτικής απόφασης για τα VM και τα Cloudlets

Για να αλλάξει η στρατηγική που ακολουθάει το simulator στον κώδικα για την αντιστοίχιση VMs σε hosts και Cloudlets σε VMs θα πρέπει να γίνουν οι εξής αλλαγές στα εξής σημεία του κώδικα.

```java
hostList.add(
         new Host(
            hostId,
            new RamProvisionerSimple(ram),
            new BwProvisionerSimple(bw),
            storage,
            peList1,
            new VmSchedulerTimeShared(peList1)
         )
      ); // This is our first machine

hostId++;

hostList.add(
         new Host(
            hostId,
            new RamProvisionerSimple(ram),
            new BwProvisionerSimple(bw),
            storage,
            peList2,
            new VmSchedulerTimeShared(peList2)
         )
      ); // Second machine
vm[i] = new Vm(i, userId, mips, pesNumber, ram, bw, size, vmm, new CloudletSchedulerTimeShared());
```

Μπορεί κανείς να διαλέξει διάφορες άλλες τεχνικές, όπως το VmScheduleSpaceShared. Κοιτάζοντας τον κώδικα και το documentation του στο διαδίκτυο παρατηρείτε μια πληθώρα από διαφορετικές στρατηγικές. Για παράδειγμα, μια εναλλακτική στρατηγική είναι το VmSchedulerTimeSharedBySubscription για την αντιστοίχιση VM σε Hosts, όπου φορτώνει όσα πιο πολλά cloudlets χωράει εκείνη τη στιγμή και όταν «γεμίσει», βάζει τα υπόλοιπα σε μία λίστα αναμονής. Για την αντιστοίχιση Cloudlets σε VM, από το CloudletSchedulerDynamicWorkload, όπου θεωρητικά δουλεύει με time-shared, με τη διαφορά ότι υποθέτει πως τουλάχιστον 1 Cloudlet είναι μία δικτυακή υπηρεσία.

## Αλλαγή πολιτικής αντιστοίχισης για τα VM και τα Cloudlets

Για να τροποποιηθεί το μοντέλο για την αντιστοίχιση VM και Cloudlets, θα αλλάξει το από κάτω αντικείμενο.

```java
UtilizationModel utilizationModel = new UtilizationModelFull();
```

 Υπάρχουν διάφορα, πρώτα ας αναλυθεί το UtilizationModelNull. Καταρχάς, το Cloudlet πάντα ζητάει μηδενική χωρητικότητα. Στα αποτελέσματα, να παρατηρηθεί ότι η διάρκεια που λειτουργεί κάθε Cloudlet έχει αυξηθεί κατά 1 δευτερόλεπτο. Δηλαδή, παίρνει 2 δευτερόλεπτα. Υπάρχει επίσης το UtilizationModelStohastic όπου το κάθε Cloudlet παίρνει μία τυχαία τιμή κάθε πλαίσιο.

## Custom παράδειγμα

Το custom παράδειγμα είναι ένας συνδιασμός του έκτου παραδείγματος με του όγδοου. Έχει δωθεί SpaceShared για τον scheduler των Cloudset, ενώ για την αντιστοίχηση των hosts με τα VM έχει δωθεί TimeShared. Έτσι, κληρωνομεί τα πλεοκεκτήματα του TimeShared (δηλαδή να χωρίζει και να δουλεύει παρόμοια με το RoundRobin), ενώ παράλληλα έχει μια πιο κατανεμημένη οργάνωση στην αντιστοίχηση των Cloudlet με τα VM.
