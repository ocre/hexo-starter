---
title: 身份证号输入框中间用星号显示
date: 2020-10-19 16:08:32
tags:
- Javascript
---

接到一个需求，有一个输入框，用来显示用户的身份证号。要求显示时只显示身份证号的前4位和后4位，中间用星号代替。支持用户修改身份证号。

实现思路如下：
1. 写两个input输入框，姑且称为input1和input2，input1用来保存正确的身份证号、表单验证、提交都读取input1的值。input2用来显示星号处理过的身份证号，并处理用户按键编辑。
2. 给input2绑定3个事件，keyup、focusin、focusout。focusin时从input1读取正确身份证号填充给input2，keyup时将用户修改后的身份证号写回到input1，focusout时触发input1的表单验证。

具体代码如下：
首先，上index.html
```html
<form id="dataForm">
	<ul>
		<li>
			<p class="word">身份证:</p>
			<p>
				<input type="text" class="int-text valid" id="p_legalCertNum" name="plegalCertNum" value="" aria-invalid="false" />
				<input type="text" style="height: 0.5px;width: 0;padding: 0;margin: 0;" id="legalCertNum" name="legalCertNum" value="511102199010162531" />
			</p>
			<label id="legalCertNumError" style="display: none;">
				<span class="ash" id="legalCertNumText"></span>
			</label>
		</li>
	</ul>
</form>
```
Javascript代码如下：
```javascript
<script src="js/jquery.min.js"></script>
<script src="js/jquery.validate.min.js"></script>
<script>
$(function(){
	var formValidator=$("#dataForm").validate({
		rules: {
			legalCertNum:{
				required:false,
				regexp: /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/,
				digits:false
      }
		},
		messages: {
			legalCertNum:{
				regexp: '身份证号格式错误'
			}
		}
	});
  var $realInput = $('#legalCertNum'), $showInput = $('#p_legalCertNum'), _that = this;
  // init value on page load
  $showInput.val(this._maskIdCard($realInput.val()));
  // register events
  $showInput.bind('keyup', function(){
  $realInput.val($showInput.val());
  });
  $showInput.bind('focusin', function(){
  $(this).val($('#legalCertNum').val());
  });
  $showInput.bind('focusout', function(){
  formValidator.element($realInput[0]);
  $(this).val(_that._maskIdCard($realInput.val()));
  });
})
</script>
```
