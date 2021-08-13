通过禁止 drop 事件的浏览器默认行为，可以阻止浏览器打开或下载文件的动作。但有时可能会发现 drop 事件不能被触发，这时要注意下是否有写了 dragenter 和 dragover 事件，要阻止这两个事件的浏览器默认行为，drop 事件才能能够被触发。
```javascript
$('.drop').on('dragover',function(event){
    event.preventDefault();
})
$('.drop').on('dragenter',function(event){
    event.preventDefault();
    ...code
 });
 ```
