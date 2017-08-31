# ---monkey
前端学习分享

<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<link rel="stylesheet" href="reset.css" />
		<link rel="stylesheet" href="slider.css" />
		<script src="slider.js"></script>
	</head>
	<body>
		<div id="slider-wrap" class="slider-wrap">
			<div class="pic clearfix" id="pic">
				<ul>
					<li><img src="../img/a1.jpg"/></li>
					<li><img src="../img/a2.jpg"/></li>
					<li><img src="../img/a3.jpg"/></li>
					<li><img src="../img/a4.jpg"/></li>
					<li><img src="../img/a5.jpg"/></li>
				</ul>
					<span class="btn-prev"></span>
					<span class="btn-next"></span>
					<a href="javascript:;" class="prev-mask"></a>
					<a href="javascript:;" class="next-mask"></a>
			</div>
			<div class="dot">
				<ul>
					<li>1</li>
					<li>2</li>
					<li>3</li>
					<li>4</li>
					<li>5</li>
				</ul>
			</div>
		</div>
	</body>
</html>













window.onload=function(){
	var oSlider=document.getElementById('slider-wrap');
    var oBtnPrev=getByClass(oSlider,'btn-prev')[0];//选择左边按钮
    var oBtnNext=getByClass(oSlider,'btn-next')[0];//选择右边按钮
    var oMaskLeft=getByClass(oSlider,'prev-mask')[0];//左边遮罩层
    var oMaskRight=getByClass(oSlider,'next-mask')[0];//右边遮罩层

	var oDotUl=getByClass(oSlider,'dot')[0].getElementsByTagName('ul')[0];//获得dot下的ul
	var oDotLis=oDotUl.getElementsByTagName('li');//获得dot下的li

	var oPicUl=document.getElementById('pic').getElementsByTagName('ul')[0]//获得pic下的ul
	var oPicLis=oPicUl.getElementsByTagName('li');//获得pic下的li
	var i=0;
	var iNow=0;/*表示当前的序号*/
	var iMinZindex=2;
	var timer2=null;
	/*1.实现鼠标移入右半部分，向左右显示*/
	oBtnPrev.onmouseover=oMaskLeft.onmouseover=function(){
		startMove(oBtnPrev,'opacity',100);//鼠标移入左边mask的时候，左按钮显示
		clearInterval(timer2);
	}
	oMaskLeft.onmouseout=function(){
		startMove(oBtnPrev,'opacity',0);//鼠标移出的时候让左按钮的透明度变为0
        timer2= setInterval(function(){
            iNow++
            if(iNow==oDotLis.length) {
                iNow = 0;
            }
            tab();
        },3000)
	}
	oBtnNext.onmouseover=oMaskRight.onmouseover=function(){
		startMove(oBtnNext,'opacity',100);//鼠标移入左边mask的时候，左按钮显示
        clearInterval(timer2);
	}
	oMaskRight.onmouseout=function(){
		startMove(oBtnNext,'opacity',0);//鼠标移出的时候让左按钮的透明度变为0
		timer2= setInterval(function(){
        	iNow++
            if(iNow==oDotLis.length) {
                iNow = 0;
            }
            tab();
        },3000)

	}
	/*存在一个小问题：当鼠标移入oBtnPrev oBtnNext时候，其透明度也为0
	 原因：遮罩层与oBtn的层级  oBtn在遮罩层上边
	 解决：不经移入遮罩要显示oBtnPrev，移入oBtnPrev也要显示oBtnPrev
	 * */
	/*2.实现鼠标移入dot li其透明度发生变化*/
	for(i=0;i<oDotLis.length;i++){
		//给所有的li都加一个索引
		oDotLis[i].index=i;
		/*鼠标移入移出dot li 透明度变化*/
		oDotLis[i].onmouseover=function(){
			// alert(123)
			startMove(this,'opacity',100);
		}
		oDotLis[i].onmouseout=function(){
			if(this.index!=iNow){  //不是在当前的li移入移出的时候改变透明度
                startMove(this,'opacity',60)
			}

		}
		/*鼠标点击li透明度*/
		oDotLis[i].onclick=function(){
            iNow=this.index;
            tab();
		}/*问题：点击成了不透明，但是鼠标离开后又回复了半透明
		   原因：设置了鼠标离开事件
		   解决：在鼠标移出的时候添加判断条件   */
	}
    function  tab() {
        for(i=0;i<oDotLis.length;i++){
            startMove(oDotLis[i],'opacity',60);/*将所有的li的透明度都变为60*/
        }
        startMove(oDotLis[iNow],'opacity',100);/*当前点击的变为100*/
        var leftValue=-oPicLis[0].offsetWidth * iNow;
        startMove(oPicUl,'left',leftValue);
    }
	/*实现点击左右按钮*/
     oBtnPrev.onclick=function(){
     	iNow--;/*当前的序号-1*/
		 if(iNow==-1){
		 	iNow=oDotLis.length-1;
		 }
		 tab();
	 }
    oBtnNext.onclick=function(){
        iNow++;/*当前的序号-1*/
        if(iNow==oDotLis.length){
            iNow=0;
        }
        tab();
    }
    /*自动轮播*/
     timer2= setInterval(function(){
        iNow++;
        if(iNow==oDotLis.length) {
            iNow = 0;
        }
        tab();
    },3000)

}









/*获得元素className*/
function getByClass(oParent,sClass){
	var aEle=document.getElementsByTagName('*');/*获得所有元素*/
	var i=0;
	var aResult=[];
	for(i=0;i<aEle.length;i++){
		if(aEle[i].className==sClass){
			aResult.push(aEle[i]);
		}
	}
	return aResult;
}
/*获取作用在元素身上的样式*/
function getStyle(obj, attr) {
	if(obj.currentStyle) {
		return obj.currentStyle[attr];
	} else {
		return getComputedStyle(obj, false)[attr];
	}
}
var timer = null;
/*运动框架*/
function startMove(obj, attr, iTarget) {
	clearInterval(obj.timer);
	obj.timer = setInterval(function() {
		var iCur = 0;
		if(attr == 'opacity') {
			iCur = parseInt(parseFloat(getStyle(obj, attr)) * 100);
		} else {
			iCur = parseInt(getStyle(obj, attr));
		}

		var iSpeed = (iTarget - iCur) / 5;

		iSpeed = iSpeed > 0 ? Math.ceil(iSpeed) : Math.floor(iSpeed);

		if(iCur == iTarget) {
			clearInterval(obj.timer);
		} else {

			if(attr == 'opacity') {
				obj.style.filter = 'alpha(opacity:' + (iCur + iSpeed) + ')';
				obj.style.opacity = (iCur + iSpeed) / 100;
				//document.getElementById('text1').value = obj.style.opacity
			} else {
				obj.style[attr] = iCur + iSpeed + 'px';
			}

		}
	}, 30)

}
