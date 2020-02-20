# BootStrap

## Button样式

https://blog.csdn.net/wo_shi_LTB/article/details/79091438





# BootStrapTable

## API

https://blog.csdn.net/yapengliu/article/details/80191699

## 网友使用总结

https://www.cnblogs.com/laowangc/p/8875526.html

https://www.cnblogs.com/wangjiming/p/9370568.html





# 例子

## Loading

```html
<!-- loading -->
<div class="modal fade" id="loading" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" data-backdrop='static'>
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h4 class="modal-title" id="myModalLabel">提示</h4>
      </div>
      <div class="modal-body">
        请稍候。。。<span id="result"></span>
      </div>
    </div>
  </div>
</div>
```

```javascript
//显示
$('#loading').modal('show');
//影藏
$('#loading').modal('hide');
```

