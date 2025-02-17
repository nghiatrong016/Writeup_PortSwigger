![image](https://github.com/user-attachments/assets/56d66bce-02c9-4308-9c99-ad637ecf2f08)# Write Up exercise XSS_CSRF 

## Lab: Reflected XSS into HTML context with nothing encoded

![Image](https://github.com/user-attachments/assets/517fee09-f1f8-4a69-ac20-2bacc36418fe)

Đầu tiên khi vào website ta sẽ thấy 1 ô search box 

![Image](https://github.com/user-attachments/assets/f95930ac-50fd-4d74-9740-30654740ba3f)

Ta sẽ thử sử dụng và thấy rằng khi search bất cứ từ khóa gì thì sẽ được gán vào parameter ```search```

![Image](https://github.com/user-attachments/assets/a319447b-4260-450d-ac47-a3949512b9b9)

Ta sẽ thử dùng lệnh alert ở đây

![Image](https://github.com/user-attachments/assets/66831f92-f45f-4159-83c6-7444fdfcf3fb)


![image](https://gist.github.com/assets/91708209/e47f9631-c513-4a17-932e-425ca047db47)


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

![image](https://gist.github.com/assets/91708209/d1c0e2d4-eff4-4a92-8574-560a6499a037)



Reference:
```text
https://flaviocopes.com/links-for-javascript/


## 
```

## Lab: DOM XSS in jQuery selector sink using a hashchange event

![image](https://gist.github.com/assets/91708209/06d62b48-6cd1-4d45-8bce-661db17987ed)

Lỗi của bài này nằm ở hashchange event trong jquery nên ta sẽ tìm đoạn code có chứa event này

![image](https://gist.github.com/assets/91708209/f44e84d9-de44-48f2-b3e9-67ee38780711)

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

![image](https://gist.github.com/assets/91708209/ede79bd9-5810-4271-8b0c-67c32888bd98)

Vì vậy mình sẽ thử inject code đơn giản

![image](https://gist.github.com/assets/91708209/040302c0-da73-4ad6-8f44-80fd10a7caf9)

Điều này chứng minh bất cứ thứ gì được inject sau dâu ```#``` sẽ được thực thi, nhưng nếu ta muốn thực hiện ```zero-click``` <br>

thì ta sẽ phải ép web chạy script bằng cách dùng ```iframe```, gửi một request có body là ```iframe``` đến web và gán giá trị ép web phải load

![image](https://gist.github.com/assets/91708209/5cac2958-5047-4fe7-8a7a-294e699501e8)

Ở payload này ta sẽ lấy trang web bị lỗi cộng chuỗi với tag img kèm với lệnh print vì vậy payload sẽ giống như lúc nãy ta thử inject ở trên

![image](https://gist.github.com/assets/91708209/66cb50ce-8d34-440c-8339-3c6675454b93)


## Lab: Reflected XSS into attribute with angle brackets HTML-encoded

![image](https://gist.github.com/assets/91708209/952b4ab9-681b-4758-963b-7cee6d5cb17f)

Ở challenge này ta nhận ra bất cứ gì ta type vào search box cx đều sẽ đưa vào tag ```input```


```html
<input type="text" placeholder="Search the blog..." name="search" value="hello">
```
Thêm đó ta biết được rằng tag ```input``` cũng có các event như ```img```<br>

Vì vậy payload của ta sẽ như sau: ```"onmouseover="alert(1)```<br>
Lúc này tag input sẽ trở thành: ```<input type="text" placeholder="Search the blog..." name="search" value=""onmouseover="alert(1)">```

![image](https://gist.github.com/assets/91708209/70d18daa-9b59-4d91-a59b-3aa48e66a280)

reference:
```text
https://www.html.am/tags/html-input-tag.cfm
```

## Lab: Stored XSS into anchor href attribute with double quotes HTML-encoded

![image](https://gist.github.com/assets/91708209/02f1dcc1-aecf-48b0-819a-f1c2b0944e72)

Web này có chức năng comment vậy ta sẽ xem thử chức năng này hoạt động thế nào

![image](https://gist.github.com/assets/91708209/75e2c169-60b3-4f2b-a4e7-df09c20d6d5b)

Sau đó ta nhận ra là chức năng comment của lưu website của comment trong tag ```a```, vì vậy khi ấn vào sẽ được thực thi</br>

Ta còn biết thêm nêu muốn thực thi javascript trong attribute ```href```: ```javascript:alert(1)```

![image](https://gist.github.com/assets/91708209/8b47bdf1-9f75-4ffd-b23a-8cabd875515c)

![image](https://gist.github.com/assets/91708209/57dd17c6-877d-462b-b99b-4e71e885e923)

![image](https://gist.github.com/assets/91708209/47b48c6c-e904-4358-a138-ff7d5d5863b0)


## Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded

![image](https://gist.github.com/assets/91708209/1693ae58-2fdd-420b-a0ba-b5c1e9119b93)

Challenge này bất cứ từ gì có chữ ```script``` đều bị encode

![image](https://gist.github.com/assets/91708209/792d7194-b2da-4ced-a5c2-b87457df9e97)

Giả sử ta search 1 input bình thường, ta thấy rằng input là một biến trong đoạn code javascript

![image](https://gist.github.com/assets/91708209/f9b177b1-4142-4632-b05e-7c5ead151554)

```javascript

var searchTerms = 'test';
document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
                    
```

Ta sẽ escape ra khỏi biến và thực thi thẳng code javascript chứ không cần tag script nữa<br>
Payload lúc này sẽ như sau: ```';alert(1);//```

![image](https://gist.github.com/assets/91708209/a765e7f7-d5d2-4a2c-ab0f-15c267d148b6)

![image](https://gist.github.com/assets/91708209/29a59ced-4bdc-4c5b-ab78-7935b24b2769)


## Lab: CSRF vulnerability with no defenses

![image](https://gist.github.com/assets/91708209/65055af6-4240-4123-844a-07cd6c8b754d)

Trong challenge này mục tiêu là ép người dùng thực hiện hành động không mong muốn<br>
và trong challenge này thì trong web có 1 hành động là update email

![image](https://gist.github.com/assets/91708209/fb2bf459-ded5-42ff-831d-7eda5cab6333)

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

![image](https://gist.github.com/assets/91708209/be21b6d8-6b51-4ebd-9c81-b780b5687d05)

Sau khi dùng exploit server để gửi thì bây giờ email của người dùng đã được đổi thành email ta muốn

![image](https://gist.github.com/assets/91708209/8072e5a2-055b-48b3-9668-9255782ca561)


### Account sau khi giải bài tập

![image](https://gist.github.com/assets/91708209/6570f1c3-7d38-4cb7-9836-8c019c5cba12)






















