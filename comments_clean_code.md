Reference from Robert C. Martin Series ("Uncle" Bob)

# Comments

English:
> ***" The proper use of comments is to compensate for our failure to express ourself in code. "***

Bahasa:
> ***" Penggunaan komentar yang tepat adalah untuk menkompensasi kegagalan kami untuk mengkspresikan diri dalam kode. "***

* Apa sih maksud dari statement tersebut?

apa yang saya terima dari statement tersebut, bahwa apabila kita memang menggunakan "Comments" disuatu code maka kita sebagai Programmer gagal dalam mengekspresikan diri ke dalam suatu code.


* Mengapa mengekspresikan diri ke dalam code? 

Ya, kita sebagai programmer harus dapat mengekspresikan diri kita kedalam code. Contohnya, kita memiliki class `Merchant` dan saat kita membuat method yang dimana method tersebut hanya bertugas untuk menggambil semua data dari Database, kita menamakan `getData()`. dan kita menambahkan comments `"get merchant data from database"`, disitu kita gagal dalam mengekspresikan diri kita, coba kita menamakan method tersebut menjadi `getAllMerchantData()` atau `getAllMerchant()`. apabila kita dapat mengekspreikan kita tidak perlu membuat/meletakkan comments di code tersebut.

Bahkan di buku Clean Code oleh [namanya] mengatakan.
>***" Every time you express yourself in code, you should pat yourself on the back. Every time you write a comment, you should grimace and feel the failure of your ability of expression. "***

Jadi apabila kita memang membutuhkan Comments. Pikirkan lagi karena sebisa mungkin kita menghindari Comments disuatu code.

Di dalam buku Clean Code oleh [namanya] memberikan alasan lain mengapa kita tidak menggunakan Comments. alasan tersebut adalah Kebohongan, ya, kebohongan. Coba kita lihat, misalkan kita meletakkan Comments di suatu code dan code tersebut sudah berubah dan berkembang, apakah comments tersebut masih relevan? tentu tidak, di karenakan code sesungguhnya sudah tidak lagi melakukan yang apa dikatan oleh Comments yang ada.

* Bagaimana bila kita membuat Comments yang sudah relevan, jelas, dan sesuai tujuan?

Ya bagus, namun alangkah lebih baiknya apabila kita tidak membuat comments yang dimana kita tidak perlu mengeluarkan tenaga untuk menulis Comments, dan mengeluarkan tenaga ke Codenya saja.

Dan satu hal lagi.
>***" Inaccurate comments are far worse then no comments at all "***

Jadi apabila memang anda sangat binggung untuk mengekspresikan atau memberikan comments, lebih baik anda tidak memberikan comments sama sekali daripada.

---
# Comments Do Not Make Up for Bad Code

* Menggapa programmer menulis comments in the first place?

Motivasi yang paling umum dalam menulis Comments adalah dikarnakan code yang buruk, dan apabila anda memang menggunakan comments karena kekacauan code atau tidak terorganisir code tersebut lebih baik mengeluarkan waktu untuk membersihkan atau merapihkan code tersebut dibandingkan untuk menulis comments.

---
# Explain Yourself in Code
* Apabila anda bisa memilih, anda akan milih yang mana?

>// Check to see if the employee is eligible for full benefits
>if ((employee.flags & HOURLY_FLAG) &&(employee.age > 65))

or

>if (employee.isEligibleForFullBenefits())

Apabila saya memilih, saya akan lebih memilih yang kedua. Mengapa? karena bentuknya jelas, tanpa perlu membaca comments. dan apabila yang pertama apabila saya seorang programmer yang baru saja mempelajari programmer maka saya akan binggung dengan code yang pertama dibandingkan dengan yang kedua 

---
# Good Comments

Beberapa komentar diperlukan atau bermanfaat. Perlu diingat, bahwa satu-satunya komentar yang benar-benar baik adalah komentar yang Anda temukan untuk tidak menulis.

## Legal Comments
 Komentar seperti ini tidak boleh berupa kontrak atau buku tebal hukum. lebih baik masukkan komentar yang berbentuk/bersifat lisansi atau dokumen eksternal

>// Copyright (C) 2003,2004,2005 by Object Mentor, Inc. All rights reserved.
>// Released under the terms of the GNU General Public License version 2 or later.

## Informative Comments
Terkadang berguna untuk memberikan informasi dasar dengan komentar. Contoh,

>// Returns an instance of the Responder being tested.
>protected abstract Responder responderInstance();

komentar seperti ini kadang berguna atau bisa dibilang boleh digunakan namun akan lebih baik apabila kita menggubah nama function itu menjadi `responderBeingTested()`. yang dimana itu memperjelas tujuan function itu dan itu membuat kita tidak harus menambahkan komentar untuk memperjelas tujuan dari suatu code.

Contoh lain,
```
// format matched kk:mm:ss EEE, MMM dd, yyyy 
Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*");
```
komentar ini bertujuan untuk memperjelas atau menjelaskan bahwa format yang di inginkan dari `Regex` tersebut. namun ini akan lebih baik apabila kita menkonvert-nya ke suatu class dan dari situ kita tidak akan memperlukan kommentar untuk memperjelas.

## Explanation of Intent

......... I HAVE NO CLUE WITH THIS CHAPTER

## Clarification
Tujuan dari komentar ini adalah untuk memperjelas atau mengverifikasi, namun apabila ini suatu method yang dibuat sendiri lebih baik  di perjelas dan apabila memang ini bawaan dari standard library maka gunakan lah Clarification Comments agar mengklarifikasi tujuan dari standard library tersebut. Contoh:

~~~~
public void testCompareTo() throws Exception {
	WikiPagePath a = PathParser.parse("PageA");
	WikiPagePath ab = PathParser.parse("PageA.PageB"); 
	WikiPagePath b = PathParser.parse("PageB");
	WikiPagePath aa = PathParser.parse("PageA.PageA");
	WikiPagePath bb = PathParser.parse("PageB.PageB");
	WikiPagePath ba = PathParser.parse("PageB.PageA");

	assertTrue(a.compareTo(a) == 0);		// a == a
	assertTrue(a.compareTo(b) != 0);		// a != b 
	assertTrue(ab.compareTo(ab) == 0); 		// ab == ab 
	assertTrue(a.compareTo(b) == -1);		// a < b 
	assertTrue(aa.compareTo(ab) == -1);		// aa < ab 
	assertTrue(ba.compareTo(bb) == -1);		// ba < bb 
	assertTrue(b.compareTo(a) == 1);		// b > a 
	assertTrue(ab.compareTo(aa) == 1);		// ab > aa 
	assertTrue(bb.compareTo(ba) == 1);		// bb > ba
}
~~~~

sebelum menambahkan komentar alangkah lebih baik kita melakukan testing terlebih dahulu agar tidak terjadi kesalahan dalam menambahkan komentar dan tidak terjadi resiko kesalah pahaman.

## Warning of Consequences
Kita terkadang membutuhkan komentar yang memberikan peringgatan ke pada programmer lain. Contoh:

~~~~
// Don't run unless you
// have some time to kill.
public void _testWithReallyBigFile() {
    writeLinesToFile(10000000);
    response.setBody(testFile);
    response.readyToSend(this);
    String responseString = output.toString(); 
    assertSubString("Content-Length: 1000000000", responseString);
    assertTrue(bytesSent > 1000000000);
}
~~~~
seperti code yang ini, yang dimana memberikan **peringatan** kepada programmer lain. mungkin ada orang yang menggatakan bahwa ada cara lain selain meletakan komentar di code tersebut.

## TODO Comments
TODO Comments digunakan untuk memberitau apa yang harus dilakukan dengan code ini. Contoh:

~~~~
//TODO-MdM these are not needed
// We expect this to go away when we do the checkout model protected VersionInfo makeVersion() throws Exception
{
    return null; 
}
~~~~

Apapun TODO-nya kita tidak boleh meninggalkan kode yang buruk ke dalam aplikasi tersebut.

## Amplification
Sebuah komentar dapat digunakan untuk memperkuat pentingnya suatu variabel atau function yang mungkin tampak tidak penting dalam code. Contoh: 

~~~~
String listItemContent = match.group(3).trim();
// the trim is real important. It removes the starting 
// spaces that could cause the item to be recognized
// as another list.
new ListItemWidget(this, listItemContent, this.level + 1); 
return buildList(text.substring(match.end()));
~~~~

contoh ini memberi tau bahwa .trim() ini sangat penting karena untuk kepentingan meng-recognized sebuah data. 

## Javadocs in Public APIs
Jika anda menulis API public, maka kita harus menulis javadoc yang baik. karena potongan code tersebut akan digunakan olah banyak orang dan bersifat open source. namun mesti ingat dalam membuat comments tidak boleh menyesatkan, nonloka, dan tidak jujur

---

# Bad Comments
Sebagian besar komentar termasuk dalam kategori ini. Biasanya mereka penolong atau alasan untuk kode yang buruk atau pembenaran untuk keputusan yang tidak memadai.

## Mumbling
Jangan membuat comment yang tidak jelas, maksudnya jangan membuat komentar yang hanya **pembuatnya** yang memahami. dan jangan membuat commentar yang dimana kita sebagai programmer harus mencari module lain untuk mencari apa arti dari commentar itu.

## Redundant Commants 
Sebaiknya jangan membuat comments yang menjelaskan method atau suatu variabel. Contoh:

~~~~
// Utility method that returns when this.closed is true. Throws an exception 
// if the timeout is reached.
public synchronized void waitForClose(final long timeoutMillis) throws Exception {
	if(!closed) {
		wait(timeoutMillis); 
		if(!closed) throw new Exception("MockResponseSender could not be closed"); 
	}
}
~~~~

disini dimana comment tersebut menjelaskan code yang berada dibawahnya dan comments disini tidak memberikan informasi yang informatif dan tidak juga tidak membenarkan pandangan.

## Misleading Comments
Jangan membuat commentar yang "menyesatkan". Contoh menggunakan code dari Redundact Comments. disana dibilang akan mengembalikan saat ```this.closed``` itu berkondisi ```TRUE```. namun yang dimaksud dari comment itu adalah akan meng-return ```IF this.closed == TRUE```. dan ```IF this.closed == FALSE``` akan melakukan wait() -> menunggu dan apabila closed masih FALSE maka akan melempar exception.

Sebenernya disini kita lebih baik membaca codenya dibanding membaca comment-nya yang dimana kita bisa mengetahu lebih jelas code-nya dibanding comments-nya, dan apabila programmer pemula membaca code ini, maka comment itu akan membuat binggung si pembaca.

## Mandated Comments
Misalkan anda memiliki code seperti ini,
```
public void addCD(String title, String author,int tracks, int durationInMinutes) {
	CD cd = new CD();
	cd.title = title;
	cd.author = author;
	cd.tracks = tracks; 
	cd.duration = duration; 
	cdList.add(cd);
}
```
jangan anda berikan comment untuk setiap variable di parameter itu untuk apa contoh dengan menggunakan comment seperti ini.
~~~~
/** 
*
* @param title The title of the CD
* @param author The author of the CD
* @param tracks The number of tracks on the CD
* @param durationInMinutes The duration of the CD in minutes 
*/
~~~~

disini kita seperti memberikan "amanat" ke setiap parameter tersebut, apabila memang misalkan ```title``` disitu diperuntukkan ttitle of the CD maka bikin lah nama variabel menjadi ```titleOfTheCD``` jangan hanya ```title```.

## Journal Comments
~~~~
* Changes (from 11-Oct-2001)
* --------------------------
* 11-Oct-2001 : Re-organised the class and moved it to new package com.jrefinery.date (DG);
* 05-Nov-2001 : Added a getDescription() method, and eliminated NotableDate class (DG);
* 12-Nov-2001 : IBD requires setDescription() method, now that NotableDate class is gone (DG); Changed getPreviousDayOfWeek(), getFollowingDayOfWeek() and 
*		getNearestDayOfWeek() to correct bugs (DG);
* 05-Dec-2001 : Fixed bug in SpreadsheetDate class (DG);
* 29-May-2002 : Moved the month constants into a separate interface (MonthConstants) (DG);
* 27-Aug-2002 : Fixed bug in addMonths() method, thanks to N???levka Petr (DG);
* 03-Oct-2002 : Fixed errors reported by Checkstyle (DG);
* 13-Mar-2003 : Implemented Serializable (DG);
* 29-May-2003 : Fixed bug in addMonths method (DG);
* 04-Sep-2003 : Implemented Comparable. Updated the isInRange javadocs (DG);
* 05-Jan-2005 : Fixed bug in addYears() method (1096282) (DG); 
~~~~

Zaman dulu programmer menggunakan Journal comments sebagai `log` untuk setiap perubahan yang terjadi pada code tersebut. namun unutk zaman sekarang kita tidak perlu lagi menulis Journal Comments dan dibuang saja comments seperti ini.

## Noise Comments
Contoh Noise Comments
~~~~
/**
* Default constructor. 
*/
protected AnnualDateRule() { 

}

or 

/** The day of the month. */
private int dayOfMonth;

or 

/**
* Returns the day of the month. *
* @return the day of the month. */
public int getDayOfMonth() { 
	return dayOfMonth;
}
~~~~

## Scary Noise
~~~~
/** The name. */ 
private String name;

/** The version. */
private String version;

/** The licenceName. */
private String licenceName;

/** The version. */
private String info;
~~~~
ini adalah contoh noise comments yang sangat sangat "menakutkan". maksudnya apa tujuannya memberikan comment ke sesuatu yang dasar atau sangat obvious

## Don’t Use a Comment When You Can Use a Function or a Variable
Misalkan memiliki code seperti ini,
~~~~
// does the module from the global list <mod> depend on the
// subsystem we are part of?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))
~~~~

akan lebih baik apabil kita bisa menggubah makna dari comment itu menjadi sebuat founction atau variabel, contohnya dengan menggubah code tersebut menjadi,

~~~~
ArrayList moduleDependees = smodule.getDependSubsystems(); 
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
~~~~

apabila kita meng-reduce menjadi seperti ini maka kita tidak akan membuthuhkan sebuat comment.

## Position Markers

Posisition markers itu seperti misalkan ada code seperti ini.
~~~~
/// DATA PROCESSING ////////////////
.
.
.
.
/// CREATE MODEL ///////////////////
.
.
.
.
~~~~

jadi dia seperti memberikan petunjuk bahwa dari marker kebawah bertujuan untuk apa sampai ketemu position marker lagi maka disitu end of the markers for the previous markers. namun ini harus di buang.

## Closing Brace Comments
closing brace comments digunakan untuk memberi tau closeing brace ini untuk brace yang mana. Contoh,

~~~~
public class wc {
	public static void main(String[] args) {
		BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
		String line;
		int lineCount = 0;
		int charCount = 0;
		int wordCount = 0; 
		try {
			while ((line = in.readLine()) != null) { 
				lineCount++;
				charCount += line.length();
				String words[] = line.split("\\W"); 
				wordCount += words.length;
			} //while
			System.out.println("wordCount = " + wordCount);
			System.out.println("lineCount = " + lineCount);
			System.out.println("charCount = " + charCount);
		} // try
		catch (IOException e) { 
			System.err.println("Error:" + e.getMessage());
		} //catch
		} //main 
}
~~~~
biasanya comments ini digunakan saat ada suatu proses yang panjang dalam suatu method, namun kita bisa membuat proses ini lebih ringkas dan tanpa kita menulis clossing brace dengan meletakkan proses panjang tersebut ke dalam suatu function lain yang khusus untuk proses tersebut.

## Attributions and Bylines
`/* Added by Rick */`
Source code control sangat bagus untuk menggingatkan siapa yang menambahkan dan kapan. jadi apabila ada programmer baru masuk ke perusahaan maka mereka bisa bertanya ke pembuatnya. namun apabila sudah bertahun tahun maka ini akan menjadi sudah tidak relevan.

## Commented-Out Code
**JANGAN PERNAH MELAKUKAN INI**
~~~~
InputStreamResponse response = new InputStreamResponse();
response.setBody(formatter.getResultStream(), formatter.getByteCount());
// InputStream resultsStream = formatter.getResultStream();
// StreamReader reader = new StreamReader(resultsStream);
// response.setContent(reader.read(formatter.getByteCount()));
~~~~
apabila memang comment itu sudah tidak berguna menggapa di comment, mengapa tidak di delete saja, kan sudah tidak digunakan juga. untuk dokumentasi? mengapa anda mendokumentasikan code yang sudah tidak relevan atau tidak digunakan lagi, ini hanya memberikan kekacauan dalam code.

## HTML Comments
......... I HAVE NO CLUE WITH THIS CHAPTER. BUT, intinya ini tidak jangan membuat sebuah commentar di HTML.

## Nonlocal Information
~~~~
/**
* Port on which fitnesse would run. Defaults to <b>8082</b>. *
* @param fitnessePort
*/
public void setFitnessePort(int fitnessePort) {
	this.fitnessePort = fitnessePort; 
}
~~~~

jangan membuat comment seperti ini, karena ini hanya memberi tau bahwa default portnya adalah 8082 namun comment ini tidak dapet mengkontrol portnya. apabila memang ingin memberikan default apabila kosong parameternya, lebih baik diubah code ini menjadi.

~~~~
/**
* Port on which fitnesse would run. Defaults to <b>8082</b>. *
* @param fitnessePort
*/
public void setFitnessePort(int fitnessePort) {
	if (fitnessePort == null){
		this.fitnessePort = 8082; 	
	}
	else this.fitnessePort = fitnessePort; 
}
~~~~

## Too Much Information
Jangan meletakkan diskusi atau irrelevant description ke dalam comment anda, karena ini akan nantinya menjadi "Noise".

~~~~
/*
RFC 2045 - Multipurpose Internet Mail Extensions (MIME)
Part One: Format of Internet Message Bodies
section 6.8. Base64 Content-Transfer-Encoding
The encoding process represents 24-bit groups of input bits as 
output strings of 4 encoded characters. Proceeding from left to 
right, a 24-bit input group is formed by concatenating 3 8-bit 
input groups. These 24 bits are then treated as 4 concatenated 
6-bit groups, each of which is translated into a single digit in 
the base64 alphabet. When encoding a bit stream via the base64 
encoding, the bit stream must be presumed to be ordered with the 
most-significant-bit first. That is, the first bit in the stream 
will be the high-order bit in the first 8-bit byte, and the 
eighth bit will be the low-order bit in the first 8-bit byte, 
and so on.
*/
~~~~

## Inobvious Connection
Jangan membuat comment yang tidak jelas dan yang tidak ada hubungannya dengan code-nya. dan amat jelek apabila ada comment untuk menjelaskan comment itu sendiri.

## Function Headers
Function yang pendek tidak membutuhkan descripsi atau comment untuk mendeskripsikan. lebih baik mencari nama yang tepat untuk menamakan atau mendeskripsikan function tersebut.

## Javadocs in Nonpublic Code
belum memahami maknanya
