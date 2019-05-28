# Concurrency

Jadi sebelum kita masuk ke mengapa kita melakukan Concurrency, kita harus tau Concurrency itu apa. Concurrency itu apa? Concurrency dapat dianalogikan seperti satu orang yang melakukan banyak hal, maksudnya gimana? maksudnya adalah satu orang ini misalkan ingin melakukan Task menggambil pasport, melakukan meeting, dan menyelesaikan tugas dia. maka hal yang dia bisa lakukan secara Concurrency adalah menggambil passport, saat menunggu untuk penggambilan passport dia bisa menyicil untuk menyelesaikan tugasnya, apabila antrian sudah gilirannya maka tugas tersebut ditutup terlebih dahulu dan kembali ke task penggambilan passport, dan setelah itu bisa melaksanakan meeting yang nantinya dapat di tutup dengan menyelesaikan tugasnya. jadi yang dilakukan oleh Concurrency adalah satu orang melakukan banyak hal namun dengan secara switching / scheduling (seperti round robin scheduler).

Contoh Round Robin Scheduler:
![img1](scheduler.png)
![img2](RR.png)

## Mengapa Concurrency?
Karena Concurrency strategi decoupling yang dimana melepaskan ketergantungan antara *what* (apa) dengan *when* (kapan), maksudnya apa? misalkan ada Task A -> B -> C -> D, apabila tidak menggunakn Concurrency atau Structural maka algoritma tersebut akan melakukan Task A terlebih dahulu hingga selsai, lalu B hingga selsai, lalu C hingga selsai, dan diakhiri D hingga selsai. namun apabila kita menggunakan Concurrency, maka Task tersebut bisa berubah bisa jadi menjalankan task A terlebih dahulu hingga beberapa lama tidak sampai selsai, lalu menjalankan C tidak sampai selsai, dan seterusnya. dan berdasarkan Uncle Bob, cara Concurrency membuat sistem lebih mudah untuk dipahami dan dapat menyelesaikan masalah dengan beberapa cara.

## Mitors dan Kesalahpahaman
* Concurrency always improves performance:
    Concurrency kadang-kadang dapat meningkatkan performance, tetapi hanya ketika ada banyak waktu tunggu yang dapat dibagi antara beberapa Thread atau beberapa prosesor.
* Design does not change when writing concurrent programs.
    Bahkan, desain algoritma konkuren dapat sangat berbeda dari desain sistem single-threaded. Decoupling dari apa dari kapan biasanya memiliki efek besar pada struktur sistem.
* Understanding concurrency issues is not important when working with a container such as a Web or EJB container.
    Faktanya, Anda sebaiknya tahu apa yang sedang dilakukan dengan Container Anda dan bagaimana menjaga agar tidak terjadi update dan deadlock.

## Tantangan
let's say kita punya code seperti ini:

~~~

public class X {
    private int lastIdUsed;
    
    public int getNextId() {
     return ++lastIdUsed;
    } 
}
~~~

dan kita membuat instance of class X, dan set lastIdUsed menjadi 42 lalu membagi instance ini ke dua thread. lalu kedua thread ini memanggil `getNextId()`. menurut buku uncle bob, kemungkinan yang akan terjadi akan menjadi tiga:
* Thread one gets the value 43, thread two gets the value 44, lastIdUsed is 44.
* Thread one gets the value 44, thread two gets the value 43, lastIdUsed is 44.
* Thread one gets the value 43, thread two gets the value 43, lastIdUsed is 43.

hal ini terjadi karena untuk skenario satu, thread pertama menggambil value tersebut lalu `getNextId()` yang membuat `lastIdUsed` menjadi 43, lalu thread kedua menggambil balue tersebut yang memiliki value 43 lalu memanggil `getNextId()` yang membuat `lastIdUsed` menjadi 44. skenario ke dua dan ketiga adalah skenario yang tidak diinginkan.

## Prinsip Pertahanan Concurrency
Berikut ini adalah serangkaian prinsip dan teknik untuk mempertahankan sistem Anda dari masalah Concurrency code.

#### Single Responsibility Principle
adalah salah satu SOLID principle yang menyatakan bahwa seharusnya class atau method melakukan hanya satu hal. maka apabila terjadi perubahan disuatu class maka tidak akan memberikan effect ke klass yang lain yang "memiliki" class tersebut.

#### Corollary: Limit the Scope of Data
Seperti contoh code yang tadi, apabila kita memodifikasi attribut yang sama dari suatu object yang dipisahkan menjadi dua thread akan terjadi skenario yang seperti tadi yang disebut "Race Condition". untuk mencegah hal tersebut kita bisa menggunakan keyword `synchronized` ke `critical section` yang digunakan di object yang disharing. Semakin banyak data yang disharing yang dapat diupdate, semakin besar kemungkinan terjadi:
* Anda akan lupa untuk melindungi satu atau lebih tempat-tempat.
* Akan sulit untuk menentukan sumber kegagalan, yang sudah cukup sulit untuk ditemukan.

#### Corollary: Use Copies of Data
untuk menghindari masalah seperti yang terjadi dicontoh code tadi adalah sebisa mungkin hindari sharing the data. dan terkadang dalam beberapa situasi mungkin terjadi copy object dan memperlakukan mereka sebagai read-only. dan ini mungkin terjadi mengkopi object dari beberapa multiple thread dan di gabungkan ke satu thread.

#### Corollary: Threads Should Be as Independent as Possible
Maksud dari bagian ini adalah sebisa mungkin apabila membuat code untuk thread jangan ada sharing data antar thread sebisa mungkin menyimpan datanya secara local. dan ini membuat thread tersebut berprilaku seperti hanya ada thread tersebut di applikasi itu dan itu membuat tidak membutuhkan synchronization.

## Ketahui Library Anda
* Use the provided thread-safe collections.
* Use the executor framework for executing unrelated tasks.
* Use nonblocking solutions when possible.
* Several library classes are not thread safe.

#### Thread-Safe Collections
jadi disini apabila kita biasanya untuk menyimpan Collection kita bisa menggunakan Hashmap, List, Arrays. namun di untuk Thread kita bisa menggunakan ConcurrentHashMap dan menurut buku uncle bob ConcurrentHashMap memiliki performance yang lebih baik dibandingkan Hashmap. dan ConcurrentHashMap memungkinkan untuk membaca dan menulis secara simultan.

**ReentrantLock:** mengimplementasikan antarmuka kunci dan menyediakan sinkronisasi metode sambil mengakses sumber daya bersama

**Semaphore:** adalah sebuah struktur data komputer yang digunakan untuk menyelesaikan proses, yaitu untuk memecahkan masalah di mana lebih dari satu proses atau thread dijalankan bersamaan.

**CountDownLatch:** sebagai bantuan sinkronisasi, yang memungkinkan satu atau lebih thread menunggu , untuk menyelesaikan  suatu task yang diberikan.

## Ketahui Models Execution Anda
Basic Knowledge or definition:

**Bound Resources:** Sumber daya ukuran tetap atau angka yang digunakan dalam lingkungan bersamaan.

**Mutual Exclusion:** jaminan hanya satu proses yang mengakses sumber daya pada satu interval waktu tertentu

**Starvation:** adalah kondisi yang biasanya terjadi setelah deadlock. Proses yang kekurangan resource (karena terjadi deadlock) tidak akan pernah mendapat resource yang dibutuhkan sehingga mengalami starvation (kelaparan)

**Deadlock:** adalah suatu kondisi dimana dua proses atau lebih saling menunggu proses yang lain untuk melepaskan resource yang sedang dipakai.

**Livelock:** adalah keadaan dimana dua proses atau lebih saling mengubah status sebagai respon terhadap perubahan status proses yang lain tanpa mengerjakan pekerjaan yang berarti. [source](http://arcana70.blogspot.com/2014/04/kbp-pert-1.html)

Contoh Deadlock: 
![img](./Picture1.png)
let say, kita punya thread dengan A memiliki 3 resource 3 dan membutuhkan 9 untuk meng-release resource dia, untuk B memiliki 2 dan membutuhkan resource 4 untuk meng-release resourcenya, dan C yang memiliki resource 2 dan membutuhkan 7 untuk meng-release resourcenya, dan yang sekarang kita miliki adalah sebanyak 3 resource. nah contoh deadlock itu saat resource yang kita miliki di berikan ke A atau ke C yang dimana apabila kita kasi semua resource kita ke salah satu dari kedua resource itu, maka akan terjadi deadlock yang nanti akan terjadi Starvation. 

Note, untuk gambar diatas adalah memory allocation yang berhasil.

#### Producer-Consumer
Producer-Consumer disini memiliki konsep yang seperti yang ada di message broker yang dimana Producer akan meng-publish suatu pesan yang dimana nanti akan masuk kedalam queue atau antrian, dan queue antara producer dan konsumer adalah *bound resource*. yang dimana Producer akan memasukkan pesan ke dalam broker yang dimana akan masuk ke dalam antrian, yang nantinya akan memberikan sinyal ke consumer yang dimana memberi tau bahwa ada sinyal dari broker bahwa ada pesan, dan setelah itu pesan akan dilanjutkan ke consumer yang dimana akan menerima pesan.

#### Reader-Writer
Readers and Writers adalah problem yang memodelkan proses yang mengakses resource yang sama. Masalah ini timbul ketika ada dua proses atau lebih berbagi data yang sama. Data yang dimaksud disini bisa berbentuk buffer, file atau objek dari suatu program. Solusi untuk problem ini dengan, Reader tidak akan menunggu reader(s) lain yang sedang membaca, walaupun ada writer yang sedang menunggu. Dengan kata lain, Jika ada reader yang datang ketika reader lain sedang membaca dan sebuah writer sedang menunggu, maka reader yang baru datang tersebut akan langsung mendapat giliran untuk membaca. Writer akan ditunda pengerjaannya. [source](http://theresia-lulu-fst13.web.unair.ac.id/artikel_detail-103396-Tugas%20Sistem%20Operasi-Problem%20Klasik%20Pada%20Sinkronisasi%20(%20Tugas%20Kelompok%20).html)

#### Dining Philosophers
Dalam masalah Dining Philosophers, diketahui sejumlah (N) filusuf yang hanya memiliki tiga status, berpikir, lapar, dan makan. Semua filusuf berada di sebuah meja makan bundar yang ditata sehingga di depan setiap filusuf ada sebuah piring berisi mie dan di antara dua piring yang bersebelahan terdapat sebuah sumpit. [source](http://theresia-lulu-fst13.web.unair.ac.id/artikel_detail-103396-Tugas%20Sistem%20Operasi-Problem%20Klasik%20Pada%20Sinkronisasi%20(%20Tugas%20Kelompok%20).html)

Solusi Dining – Philosophers Problem ada dua, yakni :
* Solusi Waiter
    Solusi Waiter : solusi sederhana ini dilakukan dengan mengadakan seorang waiter yang senantiasa mengawasi penggunaan sumpit di meja makan. Ketika empat buah (dua pasang) sumpit sedang dipakai,orang berikutnya yang ingin memakai sumpit harus meminta izin kepada sang waiter, yang hanya dapat diberi ketika salah satu sumpit telah selesai terpakai.

* Solusi Hierarki Resource
    Solusi Hirarki Resource: resources (sumpit) di meja makan telah diberi susunan hirarki. Setiap permintaan orang terhadap sebuah sumpit harus dilakukan pada susunan tertentu, dan dikembalikan pada susunan sebaliknya. Dalam hal ini, setiap orang dapat mengambil sumpit dimanapun diatas meja. Misalkan setiap sumpit diberi nomor sebagai tingkat hirarki dari 1 sampai 5, seseorang hanya dapat mengambil sumpit dengan nomor yang paling rendah, kemudian mengambil sumpit yang setingkat lebih tinggi. Ketika ia hendak mengembalikannya, orang itu harus meletakkan sumpit dengan nomor yang lebih tinggi terlebih dahulu, lalu yang rendah.

## Beware Dependencies Between Synchronized Methods
Akan ada saat ketika Anda harus menggunakan lebih dari satu metode pada objek yang disharing, contoh menggunakan anotasi synchronized lebih dari satu method didalam satu class yang dimana tugas method tersebut untuk memproteksi individual method. Ketika hal ini terjadi, ada tiga cara untuk membuat kode tersebut benar:

* Client-Based Locking: yang dimana client akan melakukan lock terhadap server sebelum memanggil method pertamanya dan pastikan lock tersebut juga terbawa sampai panggilan method terakhirnya.
* Server-Based Locking: buat method yang mengunci server, lalu memanggil semua method, lalu membuka kunci itu. jadi disini skenarionya bukan clientnnya yang menggunci melainkan servernya sendiri yang mengunci.
* Adapted Server: buat perantara yang melakukan penguncian. Ini adalah contoh dari penguncian berbasis server, di mana server asli tidak dapat diubah.

## Keep Synchronized Sections Small
saat kita menggunakan anotasi synchronized, kita memperkenalkan locks. dan locks memakan resource banyak karena akan terjadi delays. jadi sebisa mungkin membuat code dengan bagian critical sections sedikit atau seminim mungkin.

## Writing Correct Shut-Down Code Is Hard
Untuk mematikan suatu proses secara baik bisa menjadi sangat sulit di dalam multi-thread proses. masalah yang paling umum adalah Deadlock. karena bayangkan ada sebuah proses yang dimana main-thread spawn child-thread yang dimana saat main-thread ingin dimatikan, ada child-thread yang masih belum menyelesaikan proses dia. yang dimana ini dapat terjadi deadlock yang membuat sistem tidak akan bisa dimatikan.

## Testing Threaded Code
Testing Thread yang memiliki thread lebih dari dua akan membuat tasting menjadi complax. untuk tasting dalam multi-thread, ada banyak yang harus dipertimbangkan. berikut rekomendasi yang diberikan:
* Treat spurious failures as candidate threading issues.
* Get your nonthreaded code working first.
* Make your threaded code pluggable.
* Make your threaded code tunable.
* Run with more threads than processors.
* Run on different platforms.
* Instrument your code to try and force failures.

#### Treat spurious failures as candidate threading issues.
Pada dasarnya yang ingin disampaikan di sini adalah semuanya bisa salah bahkan sesuatu yang "tidak bisa gagal" itu bisa saja gagal. kebanyakan developer tidak memiliki intuisi untuk mengetahui bagaimana threading berinteraksi dengan kode lain. Jangan abaikan kegagalan sistem yang hanya "satu kali" saja terjadi.

#### Get Your Nonthreaded Code Working First
pastikan code yang diluar thread bisa bekerja terlebih dahulu. Jangan mencoba untuk menyelesaikan bug non-threading dan threading secara bersamaan. Pastikan kode Anda berfungsi di luar Thread terlebih dahulu.

#### Make Your Threaded Code Pluggable
Tulis code conccurency-support yang bisa berjalan dalam beberapa configurasi:
* One Thread, Several Thread.
* Mengkonfigurasi test sehingga dapat dijalankan untuk sejumlah iterasi.

jadi buatlah code thread-base yang pulggable yang nantinya bisa dijalankan oleh berbagai konfigurasi.

#### Make Your Threaded Code Tunable
Sejak awal, temukan cara untuk mengukur waktu kinerja sistem Anda di bawah konfigurasi yang berbeda. Biarkan jumlah utas mudah disetel.

#### Run with More Threads Than Processors
lakukan Running proses dengan setting thread lebih dari proccessor atau core yang dimiliki. semakin sering melakukan itu, semakin sering akan mendapatkan error karena critical saction atau deadlock.

#### Run on Different Platforms
lakukan tasting dengan mesin yang berbeda beda, coba lakukan tasting di mesing Windows dan Mac. bisa menggunakan virtual machine untuk mensimulasikan itu.

#### Instrument Your Code to Try and Force Failures
Untuk mendapatkan error di dalam conccurency sangat lah susah, maksudnya untuk meng-replika atau menggulang skenario untuk mendapatkan error di conccurency sangat susah karena kita tidak bisa melakukan traceing terhadap itu. oleh karena itu kita bisa melakukan menambahkan kesempatan untuk mendapatkan error di conccurency code dengan cara memanggil method seperti Object.wait(), Object.sleep(), Object.yield() dan Object.priority(). Masing-masing metode ini dapat mempengaruhi urutan eksekusi, yang dimana menambah kemungkinan untuk mendapatkan kesalahan. Ada dua opsi untuk instrumentasi kode:
* hard-code
* automated

##### hard-code
Anda bisa memasukkan method-method yang tadi disebutkan secara manual atau hard-code. namun ada beberapa masalah dengan pendekatan ini pertama Anda harus secara manual menemukan tempat yang tepat untuk melakukan ini,anda harus tau call mana yang harus dipanggil.

##### Automated
Untuk melakukan secara automated kita bisa menggunakan tools seperti AOP (Aspect Oriented Programming), CGLIB, or ASM. Sebagai contoh, anda bisa menggunakan class yang memiliki single method:

~~~~
public class ThreadJigglePoint { 
    public static void jiggle() { }
}
~~~~

Anda dapat memanggil method class ini diberbagai tempat dalam kode Anda:
~~~~
public synchronized String nextUrlOrNull() {
     if(hasNext()) {
         ThreadJiglePoint.jiggle(); 
         String url = urlGenerator.next(); 
         ThreadJiglePoint.jiggle(); 
         updateHasNext(); 
         ThreadJiglePoint.jiggle(); 
         return url;
    }
    return null; 
}
~~~~

sekarang anda bisa menggubah class `ThreadJigglePoint` menjadi interface, yang dimana akan mengimplementasi ke dua class yang dimana di satu class memiliki method kosong, yang kedua bisa dengan meng-random akan call apa yang akan dipanggil antara sleeping atau yielding.