# Error Handling

Error Handling adalah suatu hal yang harus kita lakukan saat meng-develop suatu program / aplikasi, karena input abnormal (salah memasukan inputan) bisa terjadi dan juga bisa saja terjadi kesalahan pada hardware pengguna program tersebut. Error Handling itu penting, namun apabila merusak / merecohkan logik dasarnya, maka itu salah.

### Use Exceptions Rather Than Return Codes
Jaman dulu apabila kita ingin mengetahui apakah code ini meng-return suatu error kita harus melakukan dengan cara seperti ini,

```
public class DeviceController { 
    ...
    public void sendShutDown() { 
        DeviceHandle handle = getHandle(DEV1); / Check the state of the device
        if (handle != DeviceHandle.INVALID) {
        // Save the device status to the record field retrieveDeviceRecord(handle);
        // If not suspended, shut down
            if (record.getStatus() != DEVICE_SUSPENDED) {
                pauseDevice(handle); clearDeviceWorkQueue(handle); closeDevice(handle);
            } else {
                logger.log("Device suspended. Unable to shut down");
            }
        } else {
            logger.log("Invalid handle for: " + DEV1.toString()); }
    }
    ... 
}
```

namun kekuranngannya adalah ini membuat sangat terlihat berantakan. Oleh karena itu kita menggunakan throw exception saja dengan menggunakan Try-Catch, dengan contoh seperti,

```
public class DeviceController { 
    ...
    public void sendShutDown() { 
        try {
            tryToShutDown();
        } catch (DeviceShutDownError e) {
            logger.log(e); 
        }
    }

    private void tryToShutDown() throws DeviceShutDownError {
        DeviceHandle handle = getHandle(DEV1);
        DeviceRecord record = retrieveDeviceRecord(handle);
        pauseDevice(handle); 
        clearDeviceWorkQueue(handle); 
        closeDevice(handle);
    }

    private DeviceHandle getHandle(DeviceID id) { 
        ...
        throw new DeviceShutDownError("Invalid handle for: " + id.toString());
        ... 
    }
    ... 
}
```

atau ada contoh lain dari pengalaman pribadi saat membuat microservice kecil-kecilan yang dimana saya membuat function / method untuk mengecek apakah id yang dilepar benar-benar ada didatabase masing-masing domain secara REST, dan disini saya memilih skenario apabila saya memasukan ID Environment atau ID Merchant yang tidak ada didatabase mereka. dan saya membuat custom error message di-Controller dengan mengirimkan JSON kembali ke client.

**CODE**
```
@Override
    public boolean checkEnvironmentAndMerchantIdExist(String environmentId, String merchantId) {
        GeneralDTO general = null;

        try {
            general = restTemplate.getForObject(REST_SERVICE_URI_ENVIRONMENT + "/environments/" + environmentId, GeneralDTO.class);
            general = restTemplate.getForObject(REST_SERVICE_URI_MERCHANT + "/merchants/" + merchantId, GeneralDTO.class);
        } catch (HttpClientErrorException e) {
            return false;
        }

        return true;
    }
```

**RETURN**
```
{
    "message": "Enviornment ID / Merchant ID Did't Exist",
    "isError": true
}
```
dan dengan skenario yang sama namun saya tidak menggunakan Try-Catch dan saya menggunakan IF-ELSE saya akan mendapatkan error yang sangat un-friendly yang membuat crash program saya. dengan meng-return,

**CODE**
```
@Override
    public boolean checkEnvironmentAndMerchantIdExist(String environmentId, String merchantId) {
        GeneralDTO general = null;


        general = restTemplate.getForObject(REST_SERVICE_URI_ENVIRONMENT + "/environments/" + environmentId, GeneralDTO.class);
        if (general == null) {
            return false;
        }
        
        general = restTemplate.getForObject(REST_SERVICE_URI_MERCHANT + "/merchants/" + merchantId, GeneralDTO.class);
        if (general == null) {
            return false;
        }

        return true;
    }
```
**RETURN**
```
<!doctype html><html lang="en"><head><title>HTTP Status 500 – Internal Server Error</title><style type="text/css">h1 {font-family:Tahoma,Arial,sans-serif;color:white;background-color:#525D76;font-size:22px;} h2 {font-family:Tahoma,Arial,sans-serif;color:white;background-color:#525D76;font-size:16px;} h3 {font-family:Tahoma,Arial,sans-serif;color:white;background-color:#525D76;font-size:14px;} body {font-family:Tahoma,Arial,sans-serif;color:black;background-color:white;} b {font-family:Tahoma,Arial,sans-serif;color:white;background-color:#525D76;} p {font-family:Tahoma,Arial,sans-serif;background:white;color:black;font-size:12px;} a {color:black;} a.name {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 500 – Internal Server Error</h1><hr class="line" /><p><b>Type</b> Exception Report</p><p><b>Message</b> Request processing failed; nested exception is org.springframework.web.client.HttpClientErrorException: 404 null</p><p><b>Description</b> The server encountered an unexpected condition that prevented it from fulfilling the request.</p><p><b>Exception</b></p><pre>org.springframework.web.util.NestedServletException: Request processing failed; nested exception is org.springframework.web.client.HttpClientErrorException: 404 null
	org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:965)
	org.springframework.web.servlet.FrameworkServlet.doPut(FrameworkServlet.java:866)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:663)
	org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:829)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:741)
	org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)
</pre><p><b>Root Cause</b></p><pre>org.springframework.web.client.HttpClientErrorException: 404 null
	org.springframework.web.client.DefaultResponseErrorHandler.handleError(DefaultResponseErrorHandler.java:91)
	org.springframework.web.client.RestTemplate.handleResponseError(RestTemplate.java:615)
	org.springframework.web.client.RestTemplate.doExecute(RestTemplate.java:573)
	org.springframework.web.client.RestTemplate.execute(RestTemplate.java:529)
	org.springframework.web.client.RestTemplate.getForObject(RestTemplate.java:236)
	id.dana.oauthinfo.services.OAuthInfoServiceImpl.checkEnvironmentAndMerchantIdExist(OAuthInfoServiceImpl.java:61)
	id.dana.oauthinfo.controller.OAuthInfoController.updateOAuthInfo(OAuthInfoController.java:81)
	sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	java.lang.reflect.Method.invoke(Method.java:498)
	org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:221)
	org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:137)
	org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:104)
	org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandleMethod(RequestMappingHandlerAdapter.java:743)
	org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:672)
	org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:82)
	org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:919)
	org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:851)
	org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:953)
	org.springframework.web.servlet.FrameworkServlet.doPut(FrameworkServlet.java:866)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:663)
	org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:829)
	javax.servlet.http.HttpServlet.service(HttpServlet.java:741)
	org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)
</pre><p><b>Note</b> The full stack trace of the root cause is available in the server logs.</p><hr class="line" /><h3>Apache Tomcat/9.0.17</h3></body></html>
```
---
### Write Your Try-Catch-Finally Statement First
Bagusnya Try-Catch(-Finally) adalah apabila anda terjadi error di-scope Try maka scope Try akan berakhir dititik itu dan akan dialihkan ke Catch scope.

![github-small](https://chortle.ccsu.edu/java5/Notes/chap80/catchFlowChartA.gif)

Dan di Try-Catch(-Finally) ada Finally, apabila Try-Catch menjalankan Try apabila terjadi kesalahan maka akan ke Catch dan apabila memang tidak ada error maka tidak akan ke Catch. Finally disini akan selalu dijalankan setelah Try-Catch dijalankan.

Jadi apabila terjadi error maka sequencenya akan Try -> Catch -> Finally, dan apabila tidak terjadi error maka sequencenya akan Try -> Finally.

Contoh simple Try-Catch dari w3schools [Link](https://www.w3schools.com/java/java_try_catch.asp):

**CODE**
```
public class MyClass {
  public static void main(String[ ] args) {
    try {
      int[] myNumbers = {1, 2, 3};
      System.out.println(myNumbers[10]);
    } catch (Exception e) {
      System.out.println("Something went wrong.");
    }
  }
}
```
**RETURN**
```
Something went wrong.
```

#### Tanpa Try-Catch
**CODE**
```
public class MyClass {
  public static void main(String[ ] args) {
    int[] myNumbers = {1, 2, 3};
    System.out.println(myNumbers[10]); // error!
  }
}
```

**RETURN**
```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 10
        at MyClass.main(MyClass.java:4)
```

### Use Unchecked Exceptions
**Checked Exceptions**
```
Note:
Checked Exceptions: Checked exceptions merupakan exception yang diketahui pada saat compile 
time. Namun, ada juga exceptions yang dicek pada saat compile time, oleh karena itu mereka 
disebut dengan compile time exceptions.

Contoh:
public void loadFile(String fileName){
    ...
    FileInputStream file = new FileInputStream(fileName);
    ...
}

apabila kita hanya menulis seperti ini maka akan mengeluarkan cacing merah yang tertulis 
"Unhandled Exception: java.io.FileNotFoundException" yang dimana nantinya kita memilih untuk
try-catch statement tersebut atau throws FileNotFoundException di function itu.
```

**Unchecked Exceptions**
```
Note:
Unchecked Exceptions: Unchecked exception merupakan exception yang terjadi pada saat runtime.
Pada saat compile time semuanya akan berjalan normal, namun ketika runtime maka akan 
terlempar exception ini. Oleh sebab itulah uncheked exceptions disebut juga dengan runtime 
exceptions.

Contoh:
public class TestUncheckedException{
    public static void main(String[] args){
     /*
     Pernyataan di bawah ini melemparkan NumberFormatException 
     yang termasuk dalam unchecked exception
     */
        int x = Integer.parseInt("Java");     
    }
}

saat compile time ini tidak akan memberikan cacing merah atau error apapun namun saat runtime 
akan terjadi error NumberFormatException. maka setelah itu lah baru di surround try-catch
```

***NAMUN UNTUK CONTEXT UNCLE BOB BILANG "Use Unchecked Exceptions" MASIH BELUM PAHAM.***

### Provide Context with Exceptions
Berikan client error messages dan juga exceptionya. Sebutkan apa yang gagal. misalkan menggunakan contoh

```
{
    "message": "Enviornment ID / Merchant ID Did't Exist",
    "isError": true
}
```

sebenernya ini kurang baik, karena ini hanya memberikan messagenya saya tidak memberikan exceptionnya apa, jenis kegagalannya apa.

---
(masih belom begitu paham)
### Define Exception Classes in Terms of a Caller’s Needs
Ketika kita mendefinisikan Exception Class dalam aplikasi, hal yang paling penting adalah bagaimana mereka di-catch atau di-dapatkan. coba kita liat contoh clasifikasi exception yang buruk,

```
ACMEPort port = new ACMEPort(12);

try { 
    port.open();
} catch (DeviceResponseException e) { 
    reportPortError(e);
    logger.log("Device response exception", e);
} catch (ATM1212UnlockedException e) { 
    reportPortError(e); 
    logger.log("Unlock exception", e);
} catch (GMXError e) { 
    reportPortError(e);
    logger.log("Device response exception");
} finally { 
    ...
}
```

disinikan terjadi redundansi maka kita bisa menyederhanakannya dengan membungkus ACMEPort itu ke dalam suatu class yang dimana nanti class tersebut saat `.open()` akan melakukan try-catch, Contoh.
```

LocalPort port = new LocalPort(12); 

try {
    port.open();
} catch (PortDeviceFailure e) {
    reportError(e);
    logger.log(e.getMessage(), e);
} finally {
... 
}


public class LocalPort { 
    private ACMEPort innerPort;
    
    public LocalPort(int portNumber) { 
        innerPort = new ACMEPort(portNumber);
    }
    
    public void open() { 
        try {
            innerPort.open();
        } catch (DeviceResponseException e) {
            throw new PortDeviceFailure(e);
        } catch (ATM1212UnlockedException e) {
            throw new PortDeviceFailure(e);
        } catch (GMXError e) {
            throw new PortDeviceFailure(e);
        }        
    }
    ... 
}
```

### Define the Normal Flow
Apabila anda menemukan code seperti ini,
```
try {
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID()); 
    m_total += expenses.getTotal();
} catch(MealExpensesNotFound e) { 
    m_total += getMealPerDiem();
}
```
mungkin akan sedikit bingung dengan flow yang ada di sini, sebenernya inti dari code ini kan adalah untuk mengitung total meal (m_total), apabila ada MealExpenses untuk eployee id tertentu maka m_total ditambah dengan memanggil method object espenses dan ditambahkan kedalamnya, apabila tidak ada MealExpenses maka total meal (m_total) di tambahkan dengan `getMealPerDiem()` . namun apabila menggunakan code tersebut ini akan membuat keanehan dalam flownya. oleh karena itu di refactor lah code tersebut menjadi,

```
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();
```

yang dimana kita mengubah `ExpenseReportDAO` yang dimana defaultnya akan meng-return MealExpenses object, dan apabila tidak ada meal expenses maka akan meng-return MealExpenses object yang meng-return Per Diem sebagai total. jadi kita mainkan dia di .getTotal() yang apabila memang tidak ada MealExpenses object maka akan men-return MealExpenses object yang memiliki nilai Per Diem. dan ini disebut dengan `Special Case Object`.

### Don’t Return Null
```
public void registerItem(Item item) { 
    if (item != null) {
        ItemRegistry registry = peristentStore.getItemRegistry(); 
        if (registry != null) {
            Item existing = registry.getItem(item.getID());
            if (existing.getBillingPeriod().hasRetailOwner()) {
                existing.register(item); 
            }
        } 
    }
}
```

code seperti ini buruk saat kita return null seperti yang di peristentStore, apa yang akan terjadi saat peristentStore-nya `null`? akan terjadi `NullPointerException` saat runtime. Jika anda memanggil method yang meng-return null dari third party API lebih baik di surround dengan Try-Catch atau return Special Case Object.

Contoh ini terjadi juga di experience saat code untuk microservices yang dimana saat saya memanggil method dari luar dan method yang saya panggil ini scara tidak langsung bisa meng-return null walaupun codenya seperti ini,
```
@RequestMapping(value = "/environments/{id}", method = RequestMethod.GET)
    public ResponseEntity getEnvironmentByID(@PathVariable String id) {
        EnvironmentDTO environmentDto = environmentService.findByEnvironmentId(id);

        if (environmentDto == null) {
            message = environmentService.errorMessage("OOPS, Data with ID " + id + " is not in the database");
            return new ResponseEntity<>(message, HttpStatus.NOT_FOUND);
        }
        return new ResponseEntity<>(environmentDto, HttpStatus.OK);
    }
```
namun secara tidak langsung method ini dapat mengreturn null apabila datanya tidak ada, oleh karena itu saya meng-surround saat memanggil method ini. 

### Don’t Pass Null
Let's say kita punya code seperti ini,
```
public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) { 
        return (p2.x – p1.x) * 1.5;
    }
    ... 
}
```

apayang terjadi apabila kita memanggil method tersebut dengan cara seperti ini `calculator.xProjection(null, new Point(12, 13));` hal yang akan terjadi pasti `NullPointerException`. Bagaimana kita bisa memperbaikinya? bisa dengan cara melempar Exception jika salah satu object tersebut `null` (IF-ELSE) , bisa di surround dengan Try-Catch, bisa juga dengan memberikan default value apabila tidak ingin terjadi Error.