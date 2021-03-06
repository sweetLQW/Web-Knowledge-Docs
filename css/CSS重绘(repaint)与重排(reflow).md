# CSS重绘(repaint)与重排(reflow)

## **一、重绘**

重绘是一个元素 外观的改变 所触发的浏览器行为，例如改变visibility、outline、背景色等属性。浏览器会根据元素的新属性重新绘制，使元素呈现新的外观。

## **二、重排**

重排（回流/重构）是更明显的一种改变，可以理解为渲染树需要重新计算。下面是常见的触发重排的操作：**

1. DOM元素的几何属性变化。

2. DOM树的结构变化。例如节点的增减、移动等。

3. 获取某些属性时。（当获取一些属性时，浏览器为取得正确的值也会触发重排。这样就使得浏览器的优化失效了。这些属性包括：width、height、offsetTop、offsetLeft、 offsetWidth、offsetHeight、scrollTop、scrollLeft、scrollWidth、scrollHeight、clientTop、clientLeft、clientWidth、clientHeight、getComputedStyle() (currentStyle in IE)。所以，在多次使用这些值时应进行缓存。）

4. 改变元素的一些样式，调整浏览器窗口大小，滚动条出现等等也都将触发重排。

## 三、浏览器的优化：

 重绘不一定重排，但重排一定重绘。由于浏览器在进行重绘和重排的时候是要付出高昂的性能代价的，所以很多浏览器都会优化这些操作，浏览器会维护1个队列，把所有会引起回流、重绘的操作放入这个队列，等队列中的操作到了一定的数量或者到了一定的时间间隔，浏览器就会flush队列，进行一个批处理。这样就会让多次的回流、重绘变成一次回流重绘。但是某些操作会让浏览器flush队列触发重排，例如上面所说第三点（获取某些属性时）。

   

## 如何避免浏览器重排或重绘：

1. 避免DOM及CSS选择器嵌套过深（由于css选择器解析顺序是从右向左，越具体的css解析越慢），首次重排时节省浏览器性能。
2. 将多次改变样式属性的操作合并成一次操作
3. 将需要多次重排的元素，position属性设为absolute或fixed，这样此元素就脱离了文档流，它的变化不会影响到其他元素。例如有动画效果的元素就最好设置为绝对定位。
4. 在内存中多次操作节点，完成后再添加到文档中去。例如要异步获取表格数据，渲染到页面。可以先取得数据后在内存中构建整个表格的html片段，再一次性添加到文档中去，而不是循环添加每一行。或者如果需要创建多个DOM节点，可以使用DocumentFragment创建完后一次性的加入document（方法：document.createDocumentFragment()）；
5. 由于display属性为none的元素不在渲染树中，对隐藏的元素操作不会引发其他元素的重排。如果要对一个元素进行复杂的操作时，可以先隐藏它，操作完成后再显示。这样只在隐藏和显示时触发2次重排。
6. 隐藏显示某些元素时，使用 visibility 替换 display: none，因为前者只会引起重绘，后者会引发回流（改变了布局）。
7. 需要经常取那些引起浏览器重排的属性值时，要缓存到变量。
8. 尽量不要使用 table 布局。
9. 启用GPU加速，GPU 硬件加速是指应用 GPU 的图形性能对浏览器中的一些图形操作交给 GPU 来完成，因为 GPU 是专门为处理图形而设计，所以它在速度和能耗上更有效率。GPU 加速通常包括以下几个部分：Canvas2D，布局合成, CSS3转换（`transition`），CSS3 3D变换（`transform`），WebGL和视频(`video`)。使用前一定要进行严谨的测试，否则它反而会大量占用浏览网页用户的系统资源，尤其是在移动端，肆无忌惮的开启GPU硬件加速会导致大量消耗设备电量，降低电池寿命等问题。

