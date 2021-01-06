# form表单提交跳页问题

## html5阻止

```javascript
<html>    
  <body>    
      
  <form action="" method="post" target="nm_iframe">    
      <input type="text" id="id_input_text" name="nm_input_text" />    
      <input type="submit" id="id_submit" name="nm_submit" value="提交" />    
  </form>    
      
  <iframe id="id_iframe" name="nm_iframe" style="display:none;"></iframe>    
      
  </body>    
  </html>
```

## js阻止

```javascript
submit(){
    //防止表单跳页
    event.preventDefault()
}
```

