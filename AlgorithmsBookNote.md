# 算法书笔记

## 1、go程序的一般结构

​	一般的go项目或者较复杂的程序不是简单的代码的堆加，而是有一个差不多的代码结构。即**先定义接口，然后定义结构体，最后再让结构体实现接口方法从而实现接口。**例如程序中的 arraylist/array_list.go 中的程序。

使用的时候可以创建接口类型的变量来指向实现了接口的类的实例，向上转型嘛。

## 2、go的迭代器设计模式

（以数组程序为例）



## 3、排序

### 3.1 初级排序算法

#### 3.1.1 选择排序

选择排序（Selection sort）是一种简单直观的排序算法。它的工作原理是：第一次从待排序的数据元素中选出最小（或最大）的一个元素，存放在序列的起始位置，然后再从剩余的未排序元素中寻找到最小（大）元素，然后放到已排序的序列的末尾。（将整个序列分为了两个部分，前部分是已经有序的序列，后部分无序）以此类推，直到全部待排序的数据元素的个数为零。

![选择排序](img\选择排序.png)

如上图，先从序列中选择最小的元素 5 放入序列首部，再选择剩下元素中的最小 8...

代码如下：

```go
// SelectSort 这里就简单的模拟一下
func SelectSort() {
	arr := [11]int{5, 11, 7, 2, 90, 45, 23, 8, 12, 28, 77}
	fmt.Println("选择排序前数组：", arr)
	min := arr[0]
	position := 0
	for j := 0; j < len(arr); j++ {
		min = arr[j]
		position = j
		// 找到剩余的里面最小的元素的位置
		for i := j; i < len(arr); i++ {
			if arr[i] <= min {
				min = arr[i]
				position = i
			}
		}
		// 如果剩下的找到了最小的，交换元素
		if j != position {
			arr[j], arr[position] = arr[position], arr[j]
		}

	}
	fmt.Println("选择排序后数组：", arr)
}
```



#### 3.1.2 插入排序

插入排序是指在待排序的元素中，假设前面n-1(其中n>=2)个数已经是排好顺序的，现将第n个数插到前面已经排好的序列中，然后找到合适自己的位置，使得插入第n个数的这个序列也是排好顺序的。按照此法对所有元素进行插入，直到整个序列排为有序的过程，称为插入排序。（将序列分为前后两个部分，前部分有序后部分无序，依次取无序部分元素插入到序列已经有序的前半部分，直到序列全部有序。）

代码：

```go
// InsertSort 插入排序
func InsertSort() {
	arr := [11]int{5, 11, 7, 2, 90, 45, 23, 8, 12, 28, 77}
	fmt.Println("插入排序前数组：", arr)
	// 当前待插入的元素
	position := arr[0]
	var j int
	// 外层循环
	for i := 1; i < len(arr); i++ {
		// 待插入到前面有序部分的元素
		position = arr[i]
		// 内层循环进行移位插入
		for j = i - 1; j >= 0 && arr[j] > position; j-- {
			arr[j+1] = arr[j]
		}
		arr[j+1] = position
	}
	fmt.Println("插入排序后数组：", arr)
}
```



#### 3.1.3 希尔排序

希尔排序又叫做缩小增量排序，其本质还是插入排序，只是在将待排序序列按照某种规则分成几个子序列，分别对这几个子序列进行直接插入排序，这个规则的体现就是增量的选取，如果选择增量为1那么就是直接插入排序。而希尔排序的每一趟排序都会使整个序列变得有序，等到整个序列有序了，那么再使用增量为1的插入排序的话那么就会使得排序的效率提高。

<img src="img\希尔排序.jpg" alt="希尔排序" style="zoom:80%;" />

**注：**希尔排序更高效的原因是它权衡了子数组的规模和有序性。排序之处，各个子数组都很短，排序之后子数组都是部分有序的，这两种情况都很适合插入排序。子数组的部分有序的程度取决于递增序列的选择。

和选择排序以及直接插入排序不同的是，希尔排序可以用于大型数组。它对任意排序（不一定是随机的）数组表现也很好。实际上，对于一个给定的递增序列，构造一个使希尔排序运行缓慢的数组并不容易。通过对比可以发现，希尔排序要比插入排序和选择排序快很多，且数组越大，优势越大。因为希尔排序对于中大型数组的运行时间是可以接受的且代码量很小、不需要额外的内存空间，所以有经验的程序员有时候会选择希尔排序。

**注：**Donald Shell提出了一种冲破二次时间屏障的算法Shellsort（希尔排序），在希尔排序中希尔给出了一组增量序列：ht = N / 2, h[k+1] = h[k] / 2，即{N/2, (N / 2)/2, ..., 1}，这个序列就叫做希尔增量。这个是编写希尔排序时最常用的序列，但却不是最好的。其余的增量序列还有Hibbard：{1, 3, ..., 2^k-1}，Sedgewick：{1, 5, 19, 41, 109...}该序列中的项或者是9\*4^i - 9\*2^i + 1或者是4^i - 3\*2^i + 1。使用不同的增量对希尔排序的时间复杂度的改进将不一样，甚至一点小的改变都将引起算法性能剧烈的改变。**在实际使用中，使用Sedgewick增量的希尔排序程序执行时间甚至优于使用堆排序程序。**

代码：

```go
// ShellSort 希尔排序
func ShellSort() {
	arr := [11]int{5, 11, 7, 2, 90, 45, 23, 8, 12, 28, 77}
	fmt.Println("希尔排序前数组：", arr)
	n := len(arr)
	// 步长的选取
	h := 1
	for h < n/3 {
		h = 3*h + 1
	}
	// 开始排序，控制步长
	for h >= 1 {
		// 外层循环
		for i := h; i < n; i++ {
			// 内层循环进行插入
			for j := i; j >= h && arr[j] < arr[j-h]; j -= h {
				arr[j-h], arr[j] = arr[j], arr[j-h]
			}
		}
		h = h / 3
	}
	fmt.Println("希尔排序后数组：", arr)
}
```



#### 3.1.4 冒泡排序

 冒泡排序，类似于水中冒泡，较大的数沉下去，较小的数慢慢冒起来，假设从小到大，即为较大的数慢慢往后排，较小的数慢慢往前排。

依次比较相邻的两个数，将比较小的数放在前面，比较大的数放在后面。

　　　　(1)第一次比较：首先比较第一和第二个数，将小数放在前面，将大数放在后面。

　　　　(2)比较第2和第3个数，将小数 放在前面，大数放在后面。

　　　　......

　　　　(3)如此继续，直到比较到最后的两个数，将小数放在前面，大数放在后面，重复步骤，直至全部排序完成

　　　　(4)在上面一趟比较完成后，最后一个数一定是数组中最大的一个数，所以在比较第二趟的时候，最后一个数是不参加比较的。

　　　　(5)在第二趟比较完成后，倒数第二个数也一定是数组中倒数第二大数，所以在第三趟的比较中，最后两个数是不参与比较的。

　　　　(6)依次类推，每一趟比较次数减少依次

![冒泡排序](img\冒泡排序.png)

代码：

```go
// BubbleSort 冒泡算法
func BubbleSort() {
	arr := [11]int{5, 11, 7, 2, 90, 45, 23, 8, 12, 28, 77}
	fmt.Println("冒泡排序前数组：", arr)
	for i := 0; i < len(arr); i++ {
		for j := 0; j < len(arr)-i-1; j++ {
			if arr[j] > arr[j+1] {
				arr[j], arr[j+1] = arr[j+1], arr[j]
			}
		}
	}
	fmt.Println("冒泡排序后数组：", arr)
}

```

以上代码是常规版的，存在优化的空间：例如在莫一趟之后数组已经有序了，即已经不存在元素的交换了，但是在上面的代码中仍会继续比较直到两层循环结束，所以可以设置一个标志位，当没有元素交换的时候就停止循环。

#### 3.1.5 奇偶排序

奇偶排序的核心是，以奇数列为基准和以偶数列为基准对整个数组进行排序。而排序的元素只有两个，基准元素和其右侧相邻的一个元素。原理可参见下面的算法原理图。

1. 选取所有奇数列的元素与其右侧相邻的元素进行比较，将较小的元素排序在前面；

2. 选取所有偶数列的元素与其右侧相邻的元素进行比较，将较小的元素排序在前面；

3. 重复前面两步，直到所有序列有序为止。

   ![奇偶排序]( img\奇偶排序.jpg)

**注：**奇偶排序实际上在多处理器环境中很有用，处理器可以分别同时处理每一个奇数对，然后又同时处理偶数对。因为奇数对是彼此独立的，每一刻都可以用不同的处理器比较和交换。这样可以非常快速地排序。可以增加多线程的知识再来完善这一块儿。

代码：

```go
// ODSort 奇偶排序
func ODSort() {
	arr := [11]int{5, 11, 7, 2, 90, 45, 23, 8, 12, 28, 77}
	fmt.Println("奇偶排序前数组：", arr)
	OddEvenSort(arr[:])
	fmt.Println("奇偶排序后数组：", arr)
}

// OddEvenSort 奇偶排序
func OddEvenSort(arr []int) {
	// 一个标志位，表示是否已经排序过了
	sorted := false
	// 开始循环
	for !sorted {
		sorted = true
		// 偶排序
		for i := 1; i < len(arr)-1; i += 2 {
			if arr[i] > arr[i+1] {
				arr[i], arr[i+1] = arr[i+1], arr[i]
				sorted = false
			}
		}
		// 奇排序
		for i := 0; i < len(arr)-1; i += 2 {
			if arr[i] > arr[i+1] {
				arr[i], arr[i+1] = arr[i+1], arr[i]
				sorted = false
			}
		}
	}
}

```

### 3.2 归并排序

归并排序（Merge Sort）是建立在归并操作上的一种有效，稳定的排序算法，该算法是**采用分治法**（Divide and Conquer）的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为二路归并。

归并操作的工作原理如下：
第一步：申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列
第二步：设定两个指针，最初位置分别为两个已经排序序列的起始位置
第三步：比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置
重复步骤3直到某一指针超出序列尾
将另一序列剩下的所有元素直接复制到合并序列尾

例如：初始状态：6,202,100,301,38,8,1
第一次归并后：{6,202},{100,301},{8,38},{1}，比较次数：3；
第二次归并后：{6,100,202,301}，{1,8,38}，比较次数：4；
第三次归并后：{1,6,8,38,100,202,301},比较次数：4；

**注：**归并排序速度仅次于快速排序，为稳定排序算法，一般用于对总体无序，但是各子项相对有序的数列。

代码：

```go
// MergeSort 归并排序(考虑使用递归来实现)
func MergeSort() {
	arr := [11]int{5, 11, 7, 2, 90, 45, 23, 8, 12, 28, 77}
	fmt.Println("归并排序前数组：", arr)
	bufarr := make([]int, len(arr), len(arr))
	MSort(arr[:], 0, len(arr)-1, bufarr)
	fmt.Println("归并排序后数组：", arr)
}

// MSort 归并排序
func MSort(arr []int, start int, end int, buf []int) {
	// 如果元素数量超过2个，那就在继续二分
	if start+1 < end {
		// 二分
		mid := start + (end-start)/2
		// 递归
		MSort(arr, start, mid, buf)
		MSort(arr, mid+1, end, buf)
		// 合并两个有序的数组
		Merge(arr, start, end, mid, buf)
		// 2个或者1个，只有一种条件就是左面的比右边的大才需要交换，其他条件都不需要操作
	} else if arr[start] > arr[end] {
		arr[start], arr[end] = arr[end], arr[start]
	}
}

// Merge 合并有序数组
func Merge(data []int, start int, end int, mid int, buf []int) {
	// index是合并后数组的索引，left是左半部分有序数组的索引，right是右半部分有序数组的索引
	index, left, right := start, start, mid+1
	for left != start+1 && right != end+1 {
		if data[left] > data[right] {
			buf[index] = data[right]
			index++
			right++
		} else {
			buf[index] = data[left]
			left++
			index++
		}
	}
	// 把剩下的左半部分合并到缓冲中
	for left != mid+1 {
		buf[index] = data[left]
		left++
		index++
	}
	// 把剩下的右半部分合并到缓冲中
	for right != end+1 {
		buf[index] = data[right]
		right++
		index++
	}
	// 把排好序的缓冲中的数据拷贝到数据内存中
	for i := start; i <= end; i++ {
		data[i] = buf[i]
	}
}
```

我的代码有点点问题唉，找不到，好急啊，不改了，就这样吧。

### 3.3 快速排序

快排是一种分治的排序算法。该方法的基本思想是：

- 1．先从数列中取出一个数作为基准数。
- 2．分区过程，将比这个数大的数全放到它的右边，小于或等于它的数全放到它的左边。
- 3．再对左右区间重复第二步，直到各区间只有一个数。  

虽然快速排序称为分治法，但分治法这三个字显然无法很好的概括快速排序的全部步骤。因此我的对快速排序作了进一步的说明：挖坑填数+分治法：

- 挖坑填数进行总结:

- - 1．i =L; j = R; 将基准数挖出形成第一个坑a[i]。
  - 2．j--由后向前找比它小的数，找到后挖出此数填前一个坑a[i]中。
  - 3．i++由前向后找比它大的数，找到后也挖出此数填到前一个坑a[j]中。
  - 4．再重复执行2，3二步，直到i==j，将基准数填入a[i]中。

- 示例：

- - 以一个数组作为示例，取区间第一个数为基准数。

  - | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    |
    | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
    | 72   | 6    | 57   | 88   | 60   | 42   | 83   | 73   | 48   | 85   |

  - 初始时，i = 0; j = 9;  X = a[i] = 72

  - 由于已经将 a[0] 中的数保存到 X 中，可以**理解成在数组 a[0] 上挖了个坑**，可以将其它数据填充到这来。

  - 从j开始向前找一个比X小或等于X的数。当j=8，符合条件，将a[8]挖出再填到上一个坑a[0]中。a[0]=a[8]; i++; 这样一个坑a[0]就被搞定了，但又形成了一个新坑a[8]，这怎么办了？简单，再找数字来填a[8]这个坑。这次从i开始向后找一个大于X的数，当i=3，符合条件，将a[3]挖出再填到上一个坑中a[8]=a[3]; j--;

  - 数组变为：

  - | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    |
    | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
    | 48   | 6    | 57   | 88   | 60   | 42   | 83   | 73   | 88   | 85   |

  - i = 3;  j = 7;  X=72

  - 再重复上面的步骤，先从后向前找，再从前向后找。

  - 从j开始向前找，当j=5，符合条件，将a[5]挖出填到上一个坑中，a[3] = a[5]; i++;

  - 从i开始向后找，当i=5时，由于i==j退出。

  - 此时，i = j = 5，而a[5]刚好又是上次挖的坑，因此将X填入a[5]。

  - 数组变为：

  - | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    |
    | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
    | 48   | 6    | 57   | 42   | 60   | 72   | 83   | 73   | 88   | 85   |

  - 可以看出a[5]前面的数字都小于它，a[5]后面的数字都大于它。因此再对a[0…4]和a[6…9]这二个子区间重复上述步骤就可以了。

代码：

```go
// QSort 快速排序
func QSort() {
	arr := [11]int{5, 11, 7, 2, 90, 45, 23, 8, 12, 28, 77}
	fmt.Println("快速排序前数组：", arr)
	QuickSort(arr[:])
	fmt.Println("快速排序后数组：", arr)

}

// QuickSort 快排的函数（递归）入口
func QuickSort(arr []int) {
	if len(arr) <= 1 {
		return
	}
	base := arr[0] // 初始基准元素设为第一个
	left := 0
	right := len(arr) - 1 // 右边界
	// 进入循环
	for left < right {
		// 由于左边的(第0个)被取走当成基准值，所以在左边就留下一个洞，用于存储比基准小的值
		// 所以先从右边找，找到第一个比基准值小的
		for left < right && arr[right] >= base {
			right--
		}
		// 找到了比基准值小的，那就把这个值填入左边的洞，做索引要++
		if left < right {
			arr[left] = arr[right]
			left++
		}
		// 因为上面的操作让右边留了一个洞，开始从左边找比基准值大的
		for left < right && arr[left] <= base {
			left++
		}
		// 找到比基准值大的再次把右边洞填上，并且在左边又留了一个洞
		if left < right {
			arr[right] = arr[left]
			right--
		}
	}

	// 把基准值填入到洞中，这就是本应该他所在的位置
	arr[left] = base
	// 继续分两组递归排序，注意此时基准值已经不用参与排序了
	QuickSort(arr[:left])
	QuickSort(arr[left+1:])
}
```

### 3.4 优先队列

#### 3.4.1 优先队列

普通的队列是一种先进先出的数据结构，元素在队列尾追加，而从队列头删除。在优先队列中，元素被赋予优先级。当访问元素时，具有最高优先级的元素最先删除。优先队列具有最高级先出 （first in, largest out）的行为特征。通常采用堆数据结构来实现。

优先队列是0个或多个元素的集合,每个元素都有一个优先权或值,对优先队列执行的操作有1) 查找;2) 插入一个新元素;3) 删除.在最小优先队列(min priority queue)中,查找操作用来搜索优先权最小的元素,删除操作用来删除该元素;对于最大优先队列(max priority queue),查找操作用来搜索优先权最大的元素,删除操作用来删除该元素.优先权队列中的元素可以有相同的优先权,查找与删除操作可根据任意优先权进行.

实现优先队列大致有四个实现方式：

1、 可以使用一个简单的线性表，在表头执行插入操作，并遍历该线性表删除最小元素。这样插入时间复杂度是 O(1)，而删除操作是 O(n).

2、仍是使用线性表来进行存储，可是在每次插入的时候都按照顺序插入使得线性表一直处于有序状态。这样插入的时间复杂度是O(n)，删除的时间复杂度是O(1)。

3、使用二叉查找树来实现优先队列。它对插入与删除的操作的时间复杂度均为O(logn)。但是二叉查找树对与优先队列来说又太过复杂用不太到。

4、使用二叉堆来实现优先队列。

#### 3.4.2 堆的定义

二叉堆（也叫堆）常用于实现优先队列。同二叉查找树一样，堆也有两个性质，即结构性与堆序性。对堆的所有操作都有可能破坏这两个性质，所以堆的操作必须到所有的性质都被满足的时候才能终指，就像AVL树一样。

堆是一棵被完全填满的二叉树（完全二叉树），实现上使用数组来实现数据的存储（完全二叉树的数组实现）。

下面就是堆的结构体：

```go
package heap

// Heap 堆接口，实现了就具有了堆的功能
type Heap interface {
	// ShiftUp 节点比它的父节点大（最大堆）或者小（最小堆）
	// 那么需要将它同父节点交换位置。这样是这个节点在数组的位置上升。
	ShiftUp(int)
	// ShiftDown 节点比它的子节点小（最大堆）或者大（最小堆），那么需要将它向下移动。
	ShiftDown(int)
	// Insert 在堆的尾部添加一个新的元素，然后使用 shiftUp 来修复堆。
	Insert(interface{}) error
	// Remove 移除并返回最大值（最大堆）或者最小值（最小堆）
	// 为了将这个节点删除后的空位填补上，需要将最后一个元素移到根节点的位置，然后使用 shiftDown 方法来修复堆。
	Remove() (interface{}, error)
	// RemoveIndex 和 remove() 一样，差别在于可以移除堆中任意节点，而不仅仅是根节点。
	// 当它与子节点比较位置不时无序时使用 shiftDown()，如果与父节点比较发现无序则使用 shiftUp()。
	RemoveIndex(int) (interface{}, error)
	// 将一个更小的值（最小堆）或者更大的值（最大堆）赋值给一个节点。
	// 由于这个操作破坏了堆属性，所以需要使用 shiftUp() 来修复堆属性。
	Replace(int, interface{}) error
	// Search 在堆中查找有无此元素
	Search(interface{}) (int, bool)
	// TopHeap 获取当前堆顶元素的值
	TopHeap() (interface{}, error)
}

// AHeap 使用数组实现的堆的数据结构
type AHeap struct {
	// 存储元素
	elements []interface{}
	// 容量
	cap int
	// 堆中元素个数
	size int
}

```

#### 3.4.3 堆的相关函数

代码：

```go
// NewAHeap 新建一个堆
func NewAHeap(cap int) *AHeap {
	heap := new(AHeap)
	heap.cap = cap
	heap.size = 0
	heap.elements = make([]int, cap, cap)
	return heap
}

// ShiftUp 结点位置上提
// 节点位置从 1 开始
func (heap *AHeap) ShiftUp(index int) {
	// 当前节点是不是存在父节点，没有父节点就不用上移了
	// 有父节点，如果有父节点且父节点的值小于当前节点，则将此节点上移，父节点下移
	// 一直下去（递归）直到没有父节点或者值小于了父节点
	fa := (index + 1) / 2
	// 父节点存在且当前节点大于父节点
	for fa >= 1 && heap.elements[index-1] > heap.elements[fa-1] {
		heap.elements[index-1], heap.elements[fa-1] = heap.elements[fa-1], heap.elements[index-1]
		index = fa
		fa = (index + 1) / 2
	}
}

// ShiftDown 结点位置下移
// 节点位置从 1 开始
func (heap *AHeap) ShiftDown(index int) {
	// 如果没有孩子，退出
	// 有孩子：
	//        有两个孩子，元素值小于孩子节点的值，则选择两个孩子中较小的那一个与其交换
	//        只有一个左孩子，和左孩子交换
	// 继续循环

	child1 := 2 * index
	// index 的孩子存在
	for child1 <= heap.size {
		// 有两个孩子
		if child1+1 < heap.size {
			if heap.elements[child1-1] < heap.elements[child1] {
				// 和右孩子交换
				heap.elements[index-1], heap.elements[child1] = heap.elements[child1], heap.elements[index-1]
				index = child1 + 1
				child1 = 2 * index
			} else {
				// 和右孩子交换
				heap.elements[index-1], heap.elements[child1-1] = heap.elements[child1-1], heap.elements[index-1]
				index = child1
				child1 = 2 * index
			}
		} else {
			// 只有一个孩子，那就是 child1，交换之后直接可以退出了
			heap.elements[index-1], heap.elements[child1-1] = heap.elements[child1-1], heap.elements[index-1]
			return
		}
	}
}

// Insert 在堆的尾部添加一个新的元素，然后使用 shiftUp 来修复堆。
func (heap *AHeap) Insert(value int) error {
	if heap.size >= heap.cap {
		return errors.New("堆已满，不能继续插入")
	}
	heap.elements[heap.size] = value
	heap.size++
	heap.ShiftUp(heap.size)
	return nil
}

// Remove 移除并返回最大值（最大堆）或者最小值（最小堆）
func (heap *AHeap) Remove() (interface{}, error) {
	if heap.size == 0 {
		return nil, errors.New("堆为空，无法移除")
	}
	// 取出堆顶元素，将最后一个元素移到根节点的位置，然后使用 shiftDown 方法来修复堆
	value := heap.elements[0]
	heap.elements[0] = heap.elements[heap.size-1]
	heap.size--
	heap.ShiftDown(1)
	return value, nil
}

// RemoveIndex 和 remove() 一样，差别在于可以移除堆中任意节点，而不仅仅是根节点。
// 当它与子节点比较位置不是无序时使用 shiftDown()，如果与父节点比较发现无序则使用 shiftUp()。
// 节点位置从 1 开始
func (heap *AHeap) RemoveIndex(index int) (interface{}, error) {
	if index > heap.size {
		return nil, errors.New("超出堆索引")
	}
	// 删除的是堆顶
	if index == 1 {
		return heap.Remove()
	}
	// 删除的是堆的最后一个元素，那就直接删除
	if index == heap.size {
		value := heap.elements[index-1]
		heap.size--
		return value, nil
	}
	// 删除的是堆中间的元素，则要把堆的最后一个元素提到这儿来然后进行堆得调整
	value := heap.elements[index-1]
	heap.elements[index-1] = heap.elements[heap.size-1]
	heap.size--
	// 如果与父节点比较发现无序则使用 shiftUp()
	if heap.elements[index/2-1] < heap.elements[index-1] {
		heap.ShiftUp(index)
	}
	// 与子节点比较，无序则使用 shiftDown() ，这里也要分有一个还是有两个子节点
	// 有两个
	if index*2+1 <= heap.size {
		if heap.elements[2*index-1] > heap.elements[index-1] || heap.elements[2*index+1] > heap.elements[index-1] {
			heap.ShiftDown(index)
		}
		// 有一个
	} else if index*2 == heap.size {
		if heap.elements[2*index-1] > heap.elements[index-1] {
			heap.ShiftDown(index)
		}
	}
	// 没有孩子就不用再做别的了，直接返回
	return value, nil
}

// Replace 将一个更小的值（最小堆）或者更大的值（最大堆）赋值给一个节点。
// 由于这个操作破坏了堆属性，所以需要使用 shiftUp() 来修复堆属性。
func (heap *AHeap) Replace(index int, value int) error {
	if index > heap.size {
		return errors.New("超出堆索引")
	}
	// 开始替换
	if heap.elements[index-1] > value {
		// 用了个小的替换，则需要向下调整
		heap.ShiftDown(index)
	} else {
		// 用了个大的替换，则要向上调整
		heap.ShiftUp(index)
	}
	return nil
}

// Search 在堆中查找有无此元素，返回查找到的元素的地址和 true/false
func (heap *AHeap) Search(value int) (int, bool) {
	// 堆是空的或者查找的元素比最大堆的堆顶元素都大，那就查不到
	if heap.size == 0 || value > heap.elements[0] {
		return 0, false
	}
	// 就遍历一次搜索吧，不考虑太复杂的了
	for i := 0; i < heap.size; i++ {
		// 比最大的元素都大，那下面肯定没有了
		if heap.elements[i] == value {
			return i, true
		}
	}
	return 0, false
}

// TopHeap 获取当前堆顶元素的值
func (heap *AHeap) TopHeap() (int, error) {
	if heap.size == 0 {
		return 0, errors.New("堆为空，不存在堆顶元素")
	}
	return heap.elements[0], nil
}

```

**其实 go 的标准库里有堆的接口，在包 container/heap 里，只要实现相应的接口就能够使用了。**

#### 3.4.4 堆排序

堆排序的基本思想是：将待排序序列构造成一个大顶堆，此时，整个序列的最大值就是堆顶的根节点。将其与末尾元素进行交换，此时末尾就为最大值。然后将剩余n-1个元素重新构造成一个堆，这样会得到n个元素的次小值。如此反复执行，便能得到一个有序递增序列了。

堆的构造可以直接使用 insert 函数将数组元素逐个插入，平均的时间复杂度是 O(n)而不是 O(nlogn)。

代码：

```go
// HeapSort 堆排序
func HeapSort() {
	arr := [11]int{5, 11, 7, 2, 90, 45, 23, 8, 12, 28, 77}
	fmt.Println("堆排序前数组：", arr)
	heap := heap.NewAHeap(12)
	for i := 0; i < len(arr); i++ {
		heap.Insert(arr[i])
	}
	for i := 0; i < len(arr); i++ {
		value, _ := heap.Remove()
		arr[i] = value.(int)
	}
	fmt.Println("堆排序后数组：", arr)
}
```

### 3.5 桶排序

#### 3.5.1 桶排序

桶排序的思想近乎彻底的**分治思想**。

桶排序假设待排序的一组数均匀独立的分布在一个范围中，并将这一范围划分成几个子范围（桶）。

然后基于某种映射函数f ，将待排序列的关键字 k 映射到第i个桶中 (即桶数组B 的下标i) ，那么该关键字k 就作为 B[i]中的元素 (每个桶B[i]都是一组大小为N/M 的序列 )。

接着将各个桶中的数据有序的合并起来 : 对每个桶B[i] 中的所有元素进行比较排序 (可以使用快排)。然后依次枚举输出 B[0]….B[M] 中的全部内容即是一个有序序列。

> 补充： 映射函数一般是 f = array[i] / k; k^2 = n; n是所有元素个数

为了使桶排序更加高效，我们需要做到这两点：

> 1. 在额外空间充足的情况下，尽量增大桶的数量
> 2. 使用的映射函数能够将输入的 N 个数据均匀的分配到 K 个桶中

同时，对于桶中元素的排序，选择何种比较排序算法对于性能的影响至关重要。

最基本的桶排序可以是：数据存在一个上界，例如是 M ，则可以创建一个容量为M的数组 arr，初始元素均为0。然后遍历原数组，如果元素值为 k ，则arr[k]+1。最后再遍历 arr将下标依序输出就能得到有序的序列了。

还可以创建一个链表节点的数组，然后将原数组的数据进行一个划分，然后放入对应的数组下标所对应链表内部，在每一个子链表内有序，最后再依序输出到原数组内。

![桶排序](img\桶排序.gif)

桶排序最好情况下使用线性时间O(n)，桶排序的时间复杂度，取决与对各个桶之间数据进行排序的时间复杂度，因为其它部分的时间复杂度都为O(n)。很显然，桶划分的越小，各个桶之间的数据越少，排序所用的时间也会越少。但相应的空间消耗就会增大。

#### 3.5.2 基数排序

基数排序（radix sort）属于“分配式排序”（distribution sort），又称“桶子法”（bucket sort）或bin sort，顾名思义，它是透过键值的部份资讯，将要排序的元素分配至某些“桶”中，藉以达到排序的作用，基数排序法是属于稳定性的排序，其时间复杂度为O (nlog(r)m)，其中r为所采取的基数，而m为堆数，在某些时候，基数排序法的效率高于其它的稳定性排序法。

假设有以下序列：73  22 93 43 55 14 28 65 39 81

首先根据个位数的数值，在遍历数据时将它们各自分配到编号0至9的桶（个位数值与桶号一一对应）中。

分配结果（逻辑想象）如下图所示：

![基数排序1](img\基数排序1.png)

分配结束后。接下来将所有桶中所盛数据按照桶号由小到大（桶中由顶至底）依次重新收集串起来，得到如下仍然无序的数据序列：81 22 73 93 43 14 55 65 28 39

接着，再进行一次分配，这次根据十位数值来分配（原理同上），分配结果（逻辑想象）如下图所示：

![基数排序2](img\基数排序2.png)

分配结束后。接下来再将所有桶中所盛的数据（原理同上）依次重新收集串接起来，得到如下的数据序列：14 22 28 39 43 55 65 73 81 93

观察可以看到，此时原无序数据序列已经排序完毕。如果排序的数据序列有三位数以上的数据，则重复进行以上的动作直至最高位数为止。

### 3.6 锦标赛排序

锦标赛排序又叫树型排序，也是用二叉树这种数据结构（**胜者树**），属于选择排序的一种。直接选择排序之所以不够高效就是因为没有把前一趟比较的结果保留下来，每次都有很多重复的比较。锦标赛排序就是要克服这一缺点。它的基本思想与体育淘汰赛类似，首先取得n个元素的关键字，进行两两比较，得到 n/2 个比较的优胜者，将其作为第一次比较的结果保留下来，然后对这些元素再进行关键值的两两比较，…，如此重复，直到选出一个关键字最小的对象为止。将这个最小的对象输出，然后在树上置为无穷大（或者使用标志位），再选择此时树上的最小对象再次输出。这样一直下去直到所有的元素都被输出完成。

所有的元素值均在叶子节点上，两两比较选最小，然后最后找到一个最小的放在根节点上，之后取出最小的那个元素并将其置为无穷大再取次最小的，这样一直下去直到所有节点都被取完。

这种算法的缺点在于：辅助存储空间较多、最大值进行多余的比较。堆排序比它优秀，可以使用堆排序。

代码：

```go
// TournamentSort 锦标赛排序
func TournamentSort() {
	arr := [11]int{5, 11, 7, 2, 90, 45, 23, 8, 12, 28, 77}
	fmt.Println("锦标赛排序前数组：", arr)
	// 调用函数进行排序
	newarr := tournamentSort(arr[:])
	fmt.Println("锦标赛排序后数组：", newarr)
}

// tournamentSort 锦标赛排序
func tournamentSort(arr []int) []int {
	// 计算存储胜者树所需要的数组大小
	length := 2
	for length < len(arr) {
		length *= 2
	}
	length *= 2
	length--
	result := make([]int, len(arr), len(arr))
	// 树结构存储数据
	data := make([]int, length, length)
	// 存储指示位，指示这个位置对应的数据的下标
	// 如果是叶子节点，那代表的就是在 data 数组中的下标
	// 如果是非叶子节点，代表的就是孩子节点中较小的那个节点的数据（叶子）下标
	//（已经取出的，不需要参与比较，将其值为 -1）
	index := make([]int, length, length)
	// 将待排序的 arr 转存入 data 数组中（树中的叶子节点位置）
	for i := 0; i <= (length-1)/2; i++ {
		if i < len(arr) {
			// 填入数据
			data[i+(length-1)/2] = arr[i]
			index[i+(length-1)/2] = i + (length-1)/2
		} else {
			// arr 的数据已经复制完毕但是有部分没填满
			data[i+(length-1)/2] = 0
			index[i+(length-1)/2] = -1
		}
	}
	// fmt.Println("转移后的数据：", data)
	// fmt.Println("是否为无穷：", index)
	// 开始调用构造函数，构造树（对数组前半部份的元素进行写入/修改）
	// 也即将这个数组修改为一棵树
	// 构造函数在构造的时候，数组的前半部分不放元素值，而是放叶子节点在数组的下标（构造的时候要依据是否为无穷的数组）
	for i := 0; i < len(arr); i++ {
		// 调用构造函数构造出树
		reshape(data[:], index[:])
		// fmt.Println("一次构造后的数组：", data)
		// fmt.Println("一次构造后是否为无穷：", index)
		// 取出树上的根节点并把数值所在的叶子节点的 index 对应的置为无穷
		index[index[0]] = -1
		// 放入 result 数组内
		result[i] = data[0]
	}
	return result
}

// reshape 构造出一棵胜者树的过程
func reshape(data []int, index []int) {
	// 获取数组长度
	length := len(data)
	// 循环写入数组的前半部份
	for i := length/2 - 1; i >= 0; i-- {
		// 如果叶子都是无穷大（即叶子对应的 index 值都为 -1）则 data 此位置无意义，index此位置置为 -1
		if index[2*i+1] == -1 && index[2*i+2] == -1 {
			index[i] = -1
		} else if index[2*i+1] == -1 && index[2*i+2] != -1 {
			// 左孩子提出来过了（无穷大），右孩子存在
			index[i] = index[2*i+2]
			data[i] = data[2*i+2]
		} else if index[2*i+1] != -1 && index[2*i+2] == -1 {
			// 右孩子提出来过了无穷大），左孩子存在
			index[i] = index[2*i+1]
			data[i] = data[2*i+1]
		} else {
			// 左右都存在。哪个小取哪个(一样的话先取左边)
			if data[2*i+1] <= data[2*i+2] {
				index[i] = index[2*i+1]
				data[i] = data[2*i+1]
			} else {
				index[i] = index[2*i+2]
				data[i] = data[2*i+2]
			}
		}
	}
}

```

### 3.7 鸡尾酒排序

鸡尾酒排序又称双向冒泡排序、鸡尾酒搅拌排序、搅拌排序、涟漪排序、来回排序或快乐小时排序, 是冒泡排序的一种变形。鸡尾酒排序的原理跟冒泡排序差不多，只不过冒泡排序每一轮的比较都是从左至右依次比较，而鸡尾酒排序则是一轮从左至右比较，下一轮从右至左比较。

鸡尾酒排序相当于冒泡排序来说，优势是减少了排序的轮数，虽然鸡尾酒排序的最坏时间复杂度还是O(n²/2)，但在大多数情况下（除最坏以外）都比冒泡排序效率高很多，而缺点就是代码量增加了几乎一倍。

代码：

```go
// CockTailSort 鸡尾酒排序
func CockTailSort() {
	arr := [11]int{5, 11, 7, 2, 90, 45, 23, 8, 12, 28, 77}
	fmt.Println("鸡尾酒排序前数组：", arr)
	// 两个标志位，标识数组的首与尾
	i, j := 0, len(arr)-1
	// 开始循环
	for i < j {
		// 向大的冒泡，从 i 开始冒到 j 结束，冒泡完 j --
		for k := i; k < j; k++ {
			if arr[k] > arr[k+1] {
				arr[k], arr[k+1] = arr[k+1], arr[k]
			}
		}
		j--
		if i == j {
			break
		}
		// 向小的冒泡，从 j 到 i 结束，冒泡完 i ++
		for k := j; k >= i; k-- {
			if arr[k] > arr[k+1] {
				arr[k], arr[k+1] = arr[k+1], arr[k]
			}
		}
		i++
		if i == j {
			break
		}
	}
	fmt.Println("鸡尾酒排序后数组：", arr)
}
```

此排序算法的优化可以参考冒泡排序的优化。

### 3.8 梳排序

梳排序（Comb sort）是一种由Wlodzimierz Dobosiewicz于1980年所发明的不稳定排序算法。梳排序是改良自冒泡排序和快速排序，其要旨在于消除乌龟，亦即在数组尾部的小数值，这些数值是造成泡沫排序缓慢的主因。

在冒泡排序中，只比较数组中相邻的二项，即比较的二项的间距（Gap）是1，梳排序提出此间距其实可大于1，改自插入排序的希尔排序同样提出相同观点。梳排序中，开始时的间距设置为数组长度，并在循环中以固定比率递减，**通常递减率设置为1.3**。在一次循环中，梳排序如同泡沫排序一样把数组从首到尾扫描一次，比较及交换两项，不同的是两项的间距不固定于1。如同快速排序和合并排序，梳排序的效率在开始时最佳，接近结束时，即进入泡沫排序时最差。如果间距变得太小时(例如小于10)，改用诸如插入排序或鸡尾酒排序等算法，则可提升整体效能。

![梳排序](img\梳排序.gif)

假设输入为

​				8 4 3 7 6 5 2 1
目标为将之变成递增排序。 因为输入长度=8，开始时设定间距为8/1.3≒6， 则比较8和2、4和1，并作交换两次。

​				8 4 3 7 6 5  2 1
​				2  4 3 7 6 5 8  1
​				2 1 3 7 6 5 8 4
第二次循环，更新间距为6/1.3≒4。比较2和6、1和5，直至7和4，此循环中只须交换一次。

​				2 1 3  7 6 5 8  4
​				2 1 3 4 6 5 8 7
接下来的每次循环，间距依次递减为3 → 2 → 1，

间距=3时，不须交换

​				2 1 3 4 6 5 8 7
间距=2时，不须交换

​				2 1 3 4 6 5 8 7
间距h=1时，交换三次

​				2 1 3 4 6 5 8 7
​				1 2 3 4  6 5 8 7
​				1 2 3 4 5 6  8 7
​				1 2 3 4 5 6 7 8
上例中，共作了六次交换以完成排序。

### 3.9 Introsort (内省排序)

Introsort 是由 David Musser 在1997年设计的排序算法。这个排序算法首先从快速排序开始，当递归深度超过一定深度（深度为排序元素数量的对数值）后转为堆排序。

C++ 的 STL 中 sort 的实现就是用的 introsort ,是对 quicksort 的一种改进，因为 quicksort 在某些情况下会是 N^2 复杂度。

STL的sort算法的优化策略：

1、  数据量大时采用QuickSort，分段递归排序。

2、  一旦分段后的数据量小于某个门槛，为避免Quick Sort的递归调用带来的额外负荷，就改用Insertion Sort。

3、  如果层次过深，还会改用HeapSort

4、  “三点中值”获取好的分割

https://blog.csdn.net/cyningsun/article/details/7547570

https://blog.csdn.net/cyningsun/article/details/7547066?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase

### 3.10 外部排序

之前的算法都是需要先将所有的数据载入内存，然后进行排序。到那时有时候需要排序的数据量非常大，载入内存再进行排序的方法并不实际，所以就需要一些外部排序的方法，使得每次载入一部分数据进行排序，排序完成后再与外部磁盘数据进行交换后再执行排序，以此往复。外部排序因为需要不断地读取磁盘数据，所以一般很慢。

#### **3.10.1 简单算法**



#### **3.10.2 多路合并**



#### **3.10.3 多相合并**



#### **3.10.4 替换选择**



## 4、 查找

### 4.1 符号表

符号表也称为字典，是存储一个键值对的数据结构。在 golang 中，主要是有 map 集合担任。同样 ，如果我们自己实现符号表，可以有以下六种实现：

![符号表](img\符号表.jpg)

#### 4.1.1 链表（顺序查找）

![链表顺序查找](img\链表顺序查找.jpg)

#### 4.1.2 有序数组的二分查找

有序数组的符号表可以使用两个数组来实现，一个数组用来存 key 值，另一个用来存 value 值，然后插入元素的时候保证 key 值数组是有序的时候就行。

![基于有序数组](img\基于有序数组.jpg)

### 4.2 二叉查找树

已做完，见 dataStructuresAndAlgorithmsInGo(数据结构与算法Go语言实现)。

### 4.3 平衡查找树

已做完，见 dataStructuresAndAlgorithmsInGo(数据结构与算法Go语言实现)。

#### 4.3.1 2-3查找树

2-3树其实就是 **3 阶 B 树**，即每个节点最多有 3 个孩子，最少有两个孩子；每个节点内 key 的个数为 1 或 2 个。之前在datastructure 里面没有好好写 B 树的代码，这里写一下。

如下即为一棵 2-3 树的示例：

![2-3树示意图](img\2-3树示意图.png)

由于其也满足左小右大的规则，所以在 2-3 树上的查找与二分查找十分类似，而且 2-3 树是一棵绝对平衡的树，任意节点到它所有的叶子节点的深度都是相等的。  

2-3 树的插入和调整的操作其实就是 3 阶 B 树的插入操作，此处就不赘述了。

代码我没有继续写，但是写了一点点：

```go
package tree

// keyNumber 为 B 树的阶数限制
const keyNumber int = 3

// TTNode 2-3 查找树的节点
type TTNode struct {
	// 因为有时候需要分裂结点，所以 keys 切片最多存放三个 key 值
	// ptrs 切片最多存放 4 个节点指针
	keys      []int     // 元素值
	ptrs      []*TTTree // 子节点指针
	fa        *TTTree   // 父节点
	eleNumber int       // 此节点的元素个数
}

// TTTree 2-3 查找树，也就是 3 阶 B 树
type TTTree struct {
	root *TTNode
}

// InitTree 初始化函数，返回一个 TTTree 的指针
func InitTree() *TTTree {
	tree := new(TTTree)
	return tree
}

```

#### 4.3.2 红黑二叉查找树

红黑树（Red Black Tree） 是一种自平衡二叉查找树，典型的用途是实现关联数组。红黑二叉查找树的基本思想是使用标准的二叉查找树（完全由 2- 节点构成）和一些额外的信息（替换 3- 节点）来表示 2-3 树。

红黑树是每个节点都带有颜色属性的二叉查找树，颜色或红色或黑色。 在二叉查找树强制一般要求以外，对于任何有效的红黑树我们增加了如下的额外要求:

性质1. 节点是红色或黑色。 [3] 

性质2. 根节点是黑色。 

性质3.所有叶子都是黑色。（叶子是NUIL节点） 

性质4. 每个红色节点的两个子节点都是黑色。（从每个叶子到根的所有路径上不能有两个连续的红色节点）

性质5. 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。 

这些约束强制了红黑树的关键性质: 从根到叶子的最长的可能路径不多于最短的可能路径的两倍长。结果是这个树大致上是平衡的。因为操作比如插入、删除和查找某个值的最坏情况时间都要求与树的高度成比例，这个在高度上的理论上限允许**红黑树在最坏情况下都是高效的**，而不同于普通的二叉查找树。 

是性质4导致路径上不能有两个连续的红色节点确保了这个结果。**最短的可能路径都是黑色节点，最长的可能路径有交替的红色和黑色节点。**因为根据性质5所有最长的路径都有相同数目的黑色节点，这就表明了**没有路径能多于任何其他路径的两倍长。**

<img src="img\红黑树1.jpg" alt="红黑树1" style="zoom:20%;" />

**红链接，**将两个 2- 节点连接起来构成一个 3- 节点，就像上图 a b之间的那样；黑链接，是 2-3 树中的普通链接。确切的说，是将 3- 节点表示为由一条左斜的红色链接（两个 2- 节点其中之一是另一个的左子节点）相连的两个 2- 节点。所以红黑树有另外一种定义：含有红黑链接并满足下列条件的二叉查找树：

​		1、红链接均为左链接；

​		2、没有任何一个节点同时和两个红链接相连；

​		3、该树是完美黑色平衡的，即任意空链接到根节点的路径上的黑链接数量相同。

满足这样定义的红黑树和相应的 2-3 树是一一对应的。

<img src="img\红黑树2.jpg" alt="红黑树2" style="zoom:20%;" />

<img src="img\红黑树3.jpg" alt="红黑树3" style="zoom:20%;" />

如上图，因为每个节点都只有一个指向自己的链接（来自父节点），所以在节点内部设置一个布尔型变量保存指向自己的链接是什么颜色，我们约定空链接为黑色。

##### 4.3.2.1 红黑树的节点结构

则红黑树的节点结构和常用函数如下：

```go
package tree

// RED 表示红节点
const RED bool = true

// BLACK 表示黑节点
const BLACK bool = false

// RBNode 红黑树的节点结构
type RBNode struct {
	key         int     // 键值
	value       int     // 相关联的值
	left, right *RBNode // 左右子树
	N           int     // 这棵子树中的节点总数
	color       bool    // 由其父节点指向它的链接的颜色
}

// RBTree 红黑树的结构体
type RBTree struct {
	root *RBNode
}

// isRed 测试一个节点和其父节点之间链接的颜色
// 是红色就返回 true，黑色就返回false
func isRed(node *RBNode) bool {
	if node == nil {
		return false
	}
	return node.color == RED
}

// size 返回以此节点为子树的节点总数
func size(node *RBNode) int {
	if node == nil {
		return 0
	}
	return node.N
}

// IsEmpty 判断树是否为空
func (tree *RBTree) IsEmpty() bool {
	if tree != nil {
		return size(tree.root) == 0
	}
	return true
}

// Get 根据 key 值获取 value
func (tree *RBTree) Get(key int) int {
	if tree.root != nil {
		return get(tree.root, key)
	}
	return 0
}

// get 根据 key 值获取 value
func get(node *RBNode, key int) int {
	if node == nil {
		return 0
	}
	if key < node.key {
		return get(node.left, key)
	} else if key > node.key {
		return get(node.right, key)
	} else {
		return node.value
	}
}

// Min 求树上最小值
func (tree *RBTree) Min() int {
	return min(tree.root).key
}

// min 求此节点下的最小值
func min(node *RBNode) *RBNode {
	if node.left == nil {
		return node
	}
	return min(node.left)
}

```

##### 4.3.2.2 红黑树的旋转操作

红黑树在执行一些操作的时候可能会出现红色的右链接或者连续的两条红链接，这个时候就要通过一些**旋转的方法来修复**。

<img src="img\红黑树的旋转.jpg" alt="红黑树的旋转" style="zoom:20%;" />

上图左半部分为左旋转，将一个红色的右链接通过旋转变为左链接。上图的右半部分是右旋转，将一个红色的左链接旋转成为一个红色的右链接。

代码如下：

```go
// rotateLeft 左旋转，将右红链接旋转成为左红链接
func rotateLeft(node *RBNode) *RBNode {
	temp := node.right
	node.right = temp.left
	temp.left = node
	temp.color = node.color // 延续上一级链接的颜色
	node.color = RED
	temp.N = node.N
	node.N = 1 + size(node.left) + size(node.right)
	return temp
}

// rotateRight 右旋转，将左红链接旋转成为右红链接
func rotateRight(node *RBNode) *RBNode {
	temp := node.left
	node.left = temp.right
	temp.right = node
	temp.color = node.color // 延续上一级链接的颜色
	node.color = RED
	temp.N = node.N
	node.N = 1 + size(node.left) + size(node.right)
	return temp
}

```

##### 4.3.2.3 红黑树的插入

在红黑树上插入新的键的时候，可以使用旋转操作帮助保证 2-3 树 和红黑树之间的一一对应的关系，即保证：有序性和完美平衡性。同时，插入算法还需要满足另外两个重要的性质：不存在两条连续的红链接和不存在红色的右链接。插入可以有大致几种情况：

1. **向单个 2- 节点中插入新键**

   一棵只含有一个键的红黑树，整个树中就一个节点（2- 节点）。插入另一个键的时候直接搜索然后插入。如果新键小于老键，则会产生一个左红节点，否则会产生一个右红节点，此时就需要进行左旋转。

   <img src="img\红黑树插入1.jpg" alt="红黑树插入1" style="zoom:15%;" />

2. **向树底部的 2- 节点插入新键**

   用和二叉查找树相同的方式向一棵红黑树中插入一个新键，会在树的底部新增一个节点。为了保证有序性，总是使用红链接将新节点和它的父节点相连。如果父节点是一个 2- 节点：如果新节点是父节点的左链接，则不需要旋转改动，父节点直接成为一个 3- 节点；如果新节点是父节点的右链接，则需要一次左旋进行修正。 

   <img src="img\红黑树插入2.jpg" alt="红黑树插入2" style="zoom:15%;" />

3. **向一棵双键树（即只有一个 3- 节点）中插入新键**

   这种插入可以分为三种情况：新键最大、新键处于树中两键之间、新键最小。每种情况对应不同的插入以及旋转策略：

   - 新键最大

     新键最大，按照左小右大的原则，直接插在根节点的右半部分，作为其右孩子即可。由于插入的链接一开始都是红色的，所以再将两条链接变黑，就可以得到一棵由三个节点组成的高度为 2 的平衡树。

   - 新键最小

     新键最小，则其会被链接到最左边的空链接上，这样就产生了两个连续的红链接。此时，只要将上层的红链接执行异步右旋转即可得到第一种情况。

   - 新键处于树中两键之间

     新键介于原来两个键值之间，则插入的时候又会产生两个连续的红链接，一条红色左链接接一条红色右链接，这时只要对最下方的红链接执行一次左旋转即可得到第二种情况。

     <img src="img\红黑树插入3.jpg" alt="红黑树插入3" style="zoom:20%;" />

4. **对上面几种情况的分析**

   从 3 中我们可知，每种情况最后都会遇到一个节点的左右链接均为红链接，所以可以有一个专门的方法 flipColors() 来转换一个节点的两个子节点的颜色。除了将子节点由红变黑之外，还需要将父节点由黑变红。这个操作和旋转操作一样，都是局部变换，不会影响整棵树的黑色平衡性。

   ```go
   // flipColors 转换一个节点的两个子节点的颜色。
   // 除了将子节点由红变黑之外，还需要将父节点由黑变红。
   func flipColors(node *RBNode) {
   	node.color = RED
   	node.left.color, node.right.color = BLACK, BLACK
   }
   ```

   根据情况总结和**删除的情况总结**：特将上述 flipColors 更新成如下代码：

   ```go
   // flipColors 转换一个节点的两个子节点的颜色。
   // 除了将子节点由红变黑之外，还需要将父节点由黑变红。
   func flipColors(node *RBNode) {
   	node.color = !node.color
   	node.left.color, node.right.color = !node.left.color, !node.right.color
   }
   ```

   

5. **向树底部的 3- 节点插入新键**

   向树的底部的 3- 节点插入新键，则会出现上面讨论过的三种场景。指向新节点的链接可能是 3- 节点的右链（此时只需要转换颜色即可），或者是左链接（进行右旋再转换颜色），或者中链接（先左旋下层链接，然后右旋上层链接，最后转换颜色）。颜色转换会使中间的节点的链接由黑变红，这就相当于将这个中间节点送入了父节点，即可以认为是在父节点中插入了一个新键，继续使用相同的方法解决即可。

   <img src="img\红黑树插入4.jpg" alt="红黑树插入4" style="zoom:20%;" />

6. **将红链接在树中向上传递**

   2-3 树中的插入算法需要我们分解 3- 节点，将中间的键插入父节点，如此这般直到遇到一个 2- 节点或者是根节点的时候停止。每次旋转之后，都要对颜色进行转换，将中间节点变红。在父节点看来，处理这样一个红色节点的方式和处理一个新插入节点完全相同，即继续把红链接移到中节点上。

   下图就将之前的几种情况进行了总结：

   <img src="img\红黑树插入5.jpg" alt="红黑树插入5" style="zoom:15%;" />

   总之，谨慎的使用左旋、右旋以及颜色转换这三个简单的操作，就能够保证插入后红黑树和 2-3 树的一一对应的关系。在沿着插入节点到根节点的路径上向上移动是所经过的每个节点完成以下操作，就可以完成插入操作：

   - 如果右子节点是红色而左子节点是黑色，进行左旋；
   - 如果左子节点是红色且它的左子节点也是红色，则进行右旋；
   - 如果左右子节点均为红色，则进行颜色转换

7. **实现代码**

   ```go
   // Insert 向树内插入一个键值对
   func (tree *RBTree) Insert(key int, value int) {
   	// 首先找 key 值存不存在，存在更新其值，否则为它新建一个节点
   	tree.root = put(tree.root, key, value)
   	// 根节点颜色置为黑
   	tree.root.color = BLACK
   }
   
   // put 节点的插入函数
   func put(node *RBNode, key int, value int) *RBNode {
   	if node == nil {
   		return &RBNode{key, value, nil, nil, 1, RED}
   	}
   	// 比较 key 值和当前节点的 key 值，大了向右找位置，小了向左找位置
   	// 相等更新当前节点的 value
   	if key > node.key {
   		node.right = put(node.right, key, value)
   	} else if key < node.key {
   		node.left = put(node.left, key, value)
   	} else {
   		node.value = value
   	}
   
   	// 接下来进行颜色的转换
   	if isRed(node.right) && !isRed(node.left) {
   		node = rotateLeft(node)
   	}
   	if node.left != nil { // node.left.left 这一块可能空指针，判断一下
   		if isRed(node.left) && isRed(node.left.left) {
   			node = rotateRight(node)
   		}
   	}
   	if isRed(node.left) && isRed(node.right) {
   		flipColors(node)
   	}
   	node.N = size(node.left) + size(node.right) + 1
   	return node
   }
   ```

##### 4.3.2.4 红黑树的删除最小

因为红黑树也满足二叉搜索树的性质，所以其最小元素也在左半部分。从树的左侧底部删除元素，可以分为两种情况：一种是从树底部的 3- 节点中删除键，这种情况是最简单的，直接删除即可；另外一种是从树底部的 2- 节点中删除，如果删除的话就会留下一个空节点，一般我们会将他们替换为一个空链接。可是此时就破坏了树的完美平衡性。所以我们要这样做：

​	为了保证我们不会删除一个 2- 节点，我们沿着左侧链接向下进行变换，确保当前节点不是 2- 节点（可能是 3- 节点或者临时的 4- 节点）。首先，根节点可能有两种情况：一是根是 2- 节点且两个子节点也都是 2- 节点，则可以直接将这三个节点变成一个 4- 节点；否则我们需要保证根节点的左子节点不是 2- 节点，如果有必要可以从它右侧的兄弟节点“借”一个键来。以上情况如下图：

![红黑树删除最小1](img\红黑树删除最小1.jpg)

在沿着左链接向下的过程中，保证以下情况之一成立：

- 当前节点的左子节点不是 2- 节点，完成；

- 当前节点的左子节点是 2- 节点而它的亲兄弟不是 2- 节点，则将左子节点的兄弟节点中的一个键移到左子节点中；

  <img src="img\红黑树删除最小3.jpg" alt="红黑树删除最小3" style="zoom:20%;" />

- 如果当前节点的左子节点和它的兄弟节点都是 2- 节点，则将左子节点、父节点中的最小值和左子节点最近的兄弟节点合并成一个 4- 节点，使父节点由 3- 节点变成 2- 节点或者由 4- 节点变为 3- 节点。 

  <img src="img\红黑树删除最小2.jpg" alt="红黑树删除最小2" style="zoom:15%;" />

  则上面后两种情况可以总结为以下函数：

  ```go
  // moveRedLeft 主要实现以下两点：
  // 当前节点的左右子节点都是2-节点，左右节点需要从父节点中借一个节点
  // 如果该节点的右节点的左节点是红色节点，说明兄弟节点不是2-节点，可以从兄弟节点中借一个   
  func moveRedLeft(node *RBNode) *RBNode {
  	// 从父结点中借一个
  	flipColors(node)
  	// 判断兄弟节点，如果是非红节点，也从兄弟节点中借一个
  	if node.right != nil {
  		if isRed(node.right.left) {
  			node.right = rotateRight(node.right)
  			node = rotateLeft(node)
  			flipColors(node)
  		}
  	}
  	return node
  }
  ```

  

在遍历的过程中执行这个过程，最后能够得到一个含有最小键的 3- 或者 4- 节点，然后直接从中删除，将 3- 变为 2- 或者将 4- 变为 3-节点。然后再回头向上分解临时的 4- 节点。

代码：

```go
// DeleteMin 删除最小元素的函数入口
func (tree *RBTree) DeleteMin() error {
	if tree.root != nil {
		// 如果根节点的左右子节点是2-节点，我们可以将根设为红节点，这样才能进行后面的 moveRedLeft 操作，因为左子要从根节点借一个
		if !isRed(tree.root.left) && !isRed(tree.root.right) {
			tree.root.color = RED
		}
		tree.root = deleteMin(tree.root)
		// 借完以后，我们将根节点的颜色复原
		if !tree.IsEmpty() {
			tree.root.color = BLACK
		}
		return nil
	}
	return errors.New("树为空，不存在最小元素！！！")
}

// deleteMin 删除最小元素
func deleteMin(node *RBNode) *RBNode {
	// 删除
	if node.left == nil {
		return nil
	}
	// 判断 node 的左节点是不是2-节点（再向坐下递归的过程中，保证之前讨论的那三种情况成立）
	if !isRed(node.left) && !isRed(node.left.left) {
		node = moveRedLeft(node)
	}
	// 递归
	node.left = deleteMin(node.left)
	// 解除临时组成的 4- 节点，更新以此节点的棵子树中的节点总数
	return balance(node)
}

// balance 解除临时组成的 4- 节点，更新以此节点的棵子树中的节点总数
func balance(node *RBNode) *RBNode {

	if isRed(node.right) {
		node = rotateLeft(node)
	}
	// TODO: 下面这个 if 可以去掉吧
	if isRed(node.right) && !isRed(node.left) {
		node = rotateLeft(node)
	}
	if node.left != nil { // node.left.left 这一块可能空指针，判断一下
		if isRed(node.left) && isRed(node.left.left) {
			node = rotateRight(node)
		}
	}
	if isRed(node.left) && isRed(node.right) {
		flipColors(node)
	}
	node.N = size(node.left) + size(node.right) + 1
	return node
}

```

##### 4.3.2.5 红黑树删除最大

红黑树删除最大元素的方法思路类似删除最小元素，即在向右链接查找的过程中不断地进行变换操作，使得当前节点均不是 2- 节点。直到查找到最右侧然后删除即可。删除之后再向上回溯分解剩余的 4- 节点。

但是需要注意的是，由于红链接都是左链接，所以这里用到的变换与删除最小键的变换不同。

这里，沿着右子节点向下的过程中，需要保证以下情况之一成立：

- 当前节点的右子节点不是 2- 节点，完成；

- 当前节点的右子节点是 2- 节点，而兄弟节点不是 2- 节点，则从右子节点的兄弟节点那儿移动一个键值到右子节点中；

  <img src="img\红黑树删除最大2.jpg" alt="红黑树删除最大2" style="zoom:15%;" />

- 如果当前节点的右子节点和它的兄的节点都是 2- 节点，则将右子节点、父亲节点中的最大值和右子节点的兄弟节点合并成为一个 4- 节点，使父节点由 3- 节点变成 2- 节点或者由 4- 节点变为 3- 节点。 

  <img src="img\红黑树删除最大1.jpg" alt="红黑树删除最大1" style="zoom:15%;" />

  上面两种情况就总结出了以下函数，与删除最小的类似：

  ```go
  // moveRedRight 主要实现以下两个功能：
  // 当前节点的右子节点是 2- 节点，而兄弟节点不是 2- 节点，则从右子节点的兄弟节点那儿移动一个键值到右子节点中
  // 前节点的右子节点和它的兄的节点都是 2- 节点，则将右子节点、父亲节点中的最大值和右子节点的兄弟节点合并成为一个 4- 节点
  func moveRedRight(node *RBNode) *RBNode {
  	// 从父节点中取一个
  	flipColors(node)
  	// 判断兄弟节点，如果不是 2- 节点，也从兄弟节点中借一个
  	if node.left != nil {
  		if isRed(node.left.left) {
  			// 从兄弟那儿借来了一个
  			node = rotateRight(node)
  			// 从兄弟那儿取来了，那就再还给父节点一个
  			flipColors(node)
  		}
  	}
  	return node
  }
  ```

剩下的就和删除最小类似了，在遍历的过程中执行这个过程，最后能够得到一个含有最小键的 3- 或者 4- 节点，然后直接从中删除，将 3- 变为 2- 或者将 4- 变为 3-节点。然后再回头向上分解临时的 4- 节点。

```go
// DeleteMax 删除最大元素的函数入口
func (tree *RBTree) DeleteMax() error {
	if tree.root != nil {
		if !isRed(tree.root.left) && !isRed(tree.root.right) {
			tree.root.color = RED
		}
		tree.root = deleteMax(tree.root)
		if !tree.IsEmpty() {
			tree.root.color = BLACK
		}
		return nil
	}
	return errors.New("树为空，不存在最大元素！！！")
}

// deleteMax 删除最大元素
func deleteMax(node *RBNode) *RBNode {
	if isRed(node.left) {
		node = rotateRight(node)
	}
	if node.right == nil {
		return nil
	}
    // 右侧节点是 2- 节点，则在递归的时候需要执行 moveRedRight 使其满足之前说的三种情况之一
	if !isRed(node.right) && !isRed(node.right.left) {
		node = moveRedRight(node)
	}
	node.right = deleteMax(node.right)
	return balance(node)
}

```



##### 4.3.2.6 红黑树删除

**在查找路径上进行和删除最小键相同的变换操作**，同样可以**保证在被查找的过程中任意的当前节点均不是 2- 节点。**如果被查找的键在树的底部，我们可以直接删除它。如果不在底部，我们需要**将他和他的后继进行交换**，就和二叉查找树一样。因为当前节点必然不是 2- 节点，问题已经转化为在一棵节点不是 2- 节点的子树中删除最小键，我们可以直接使用之前的算法。和以前一样，删除之后我们需要向上回溯并分解剩余的 4- 节点。

代码：

```go
// Delete 删除函数，删除树上指定的 key
func (tree *RBTree) Delete(key int) error {
	if tree.root != nil {
		if !isRed(tree.root.left) && !isRed(tree.root.right) {
			tree.root.color = RED
		}
		tree.root = deleteKey(tree.root, key)
		if !tree.IsEmpty() {
			tree.root.color = BLACK
		}
		return nil
	}
	return errors.New("树为空，不存在最大元素！！！")
}

// deleteKey 删除树上指定的 key 值
func deleteKey(node *RBNode, key int) *RBNode {
	// 比较小，向左找
	if key < node.key {
		// 每当向左进行移动的时候，一定要将当前节点转换成一个非 2- 节点
		// 也就是每次向左走都要执行前面删除最小时候向左的转换 moveRedLeft
		// 删除
		if node.left == nil {
			return nil
		}
		// 判断 node 的左节点是不是2-节点（再向左下递归的过程中，保证之前讨论的那三种情况成立）
		if !isRed(node.left) && !isRed(node.left.left) {
			node = moveRedLeft(node)
		}
		// 递归
		node.left = deleteKey(node.left, key)
	} else {
		// 同样，如果需要向右走或者删除当前节点（删除当前节点需要取后继）
		// 也需要执行前面删除最大的时候向右的变换，使其当前节点不是 2- 节点
		if isRed(node.left) {
			node = rotateRight(node)
		}
        // 	这里和向右的那个有一点点区别就是，当前节点可能 key 值等于要删除的 key，所以退出的判断条件要多加一个
		if node.right == nil && key != node.key{ // 没有找到目标键，可以退出了
			return nil
		}
		// 右侧节点是 2- 节点，则在递归的时候需要执行 moveRedRight 使其满足之前说的三种情况之一
		if !isRed(node.right) && !isRed(node.right.left) {
			node = moveRedRight(node)
		}

		// 上面对节点的变换完成了，下面就开始删除
		if key == node.key {
			// 取右侧最小（后继）填入当前节点
			node.value = get(node.right, min(node.right).key)
			node.key = min(node.right).key
			// 再删除右侧最小的（后继）
			node.right = deleteMin(node.right)
		} else {
			// 这是当前节点值还是小于 key 值，继续向右找
			node.right = deleteKey(node.right, key)
		}
	}
	return balance(node)
}
```

##### 4.3.2.7 红黑树的其他操作

由于红黑树具有二叉查找树的性质，所以二叉查找树的查找最大、最小键值、select()、rank()、floor()、ceiling() 和范围查找方法不做任何变动即可继续使用，这些操作不会涉及节点颜色，也就不会影响红黑树的完美平衡性。上述的这些方法详见二叉查找树。

### 4.4 散列表

#### 4.4.1 散列表的含义

散列表是根据关键码值(Key value)而直接进行访问的数据结构，通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做散列函数，存放记录的数组叫做散列表。

若关键字为k，则其值存放在f(k)的存储位置上。由此，不需比较便可直接取得所查记录。称这个对应关系f为散列函数，按这个思想建立的表为散列表。对不同的关键字可能得到同一散列地址，即k1≠k2，而f(k1)=f(k2)，这种现象称为**冲突**（英语：Collision）。具有相同函数值的关键字对该散列函数来说称做同义词。

因此，散列表的查找算法分为两步：第一步，使用散列函数将被查找的键转换为数组的一个索引。第二步，处理冲突。理想情况下不同的键都能转换为不同的索引值，但是现实是残酷的。除非使用一个范围和键值范围一样大的数组去存储，让键值放在对应的数组位置从而不会产生冲突。但是这种方法内存的占用太高了。所以，**散列表是算法在时间和空间上进行权衡**的经典案例。

要为一个数据类型实现一个优秀的散列方法，需要满足以下几个条件：

- 一致性----等价的键必然产生相同的散列值；
- 高效性----计算简便；
- 均匀性----均匀的散列所有的键

处理冲突碰撞的方法有：拉链法、线性探测法。

#### 4.4.2 基于拉链法的散列表

处理两个或者多个键的散列值相同的情况，最直接的就是拉链法：将大小为 M 的数组中的每个元素指向一条链表，链表中的每个节点都存储了散列值为该元素的所有的键值对。

<img src="img\拉链法.png" alt="拉链法" style="zoom:85%;" />

#### 4.4.3 基于线性探查法的散列表

实现散列表的另一种方式就是使用大小为 M 的数组保存 N 个键值，其中 M > N 。我们需要依靠数组中的空位来解决冲突碰撞。基于这种策略的所有方法统称为**开放地址散列表**。

开放地址散列表中最简单的就是线性探查法。当碰撞发生的时候（一个键的散列值已经被另一个不同的键占用的时候），去检查散列表中的下一个位置（将索引值加 1 ），这样的线性探测可能会产生三种结果：

- 命中，该位置的键和被查找的键相同；
- 未命中，键为空（该位置没有键）；
- 继续查找，该位置的键和被查找的键不同。

#### 4.4.4 go 中的散列表

map 结构。详见golang基础.md



## 5、图

### 5.1 图的概念及表示

#### 5.1.1 图的基本概念

图由顶点（vertex）的集和边（Edge）的集组成。顶点代表了对象，在示意图中我们使用点或圆来表示它；边代表了两个对象的连接关系，在示意图中我们使用连接两顶点的线段来表示。

有时也把边称作弧（arc），如果点对是有序的，那么图就叫做有向的图（有向图）。如果点对是无序的，那么图就叫做无向的图（无向图）。简单的讲，边没有指向性的图叫做无向图，边具有指向性的图叫做有向图。

图中还有一些性质我就不说了。

#### 5.1.2 无向图与有向图

##### 5.1.2.1 图的几种表示方法

图主要有3种常用的存储表示方式：邻接矩阵（adjacency matrices），邻接表（adjacency lists），邻接多重表（adjacency multilists）。

**邻接矩阵**：使用一个 V 乘 V 的布尔矩阵来表示。当顶点 v 和 顶点 w 之间有相连的边的时候，定义 v 行 w 列的元素值为 true，否则为 false。但是这种方法内存占用大，顶点多的时候不实用。有向图和带权重的图都可以类似存储。

![图邻接矩阵](img\图邻接矩阵.jpg)

**邻接表**：就是用 n 个链表代替邻接矩阵中的 n 行。链表中的结点结构至少要包含一个顶点域和一个链域。对于任意给定的链表 i ，链表中的结点就是与顶点 i 相邻的所有顶点。这种是比较常用的。但是不一定使用的就是链表，也可以是其他类型的结构。

**邻接多重表**：在无向图的邻接表存储表示中，每一条边 都表示为两项：一项在顶点 的邻接表中，而另一项在顶点 的邻接表中。在多重表中，各链表中的结点可以被几个链表共享，此时图中的每一条边只对应于一个结点，而这个结点出现在该边所关联的两个顶点的每个邻接链表中。

##### 5.1.2.2 邻接表的数据结构

非稠密图的标准表称为邻接表的数据结构。它将每个顶点的所有相邻顶点都保存在该顶点对应的元素所指向的一个链表中。我们使用这个数组即可快速访问给定顶点的邻接顶点列表。

但是，在 实际实现的时候，也并非真的就是链表，也可以是 map 集合或者包等。在 go 中，可以使用map集合来创建，每个map元素的 value 对应一个切片。

##### 5.1.2.3 表示无向图的数据类型

我们可以总结得到下面图的 API：

| 返回值 | Graph                 |                                                              |
| ------ | --------------------- | ------------------------------------------------------------ |
|        | Graph(v int)          | 创建一个含有 V 个顶点但是不含有边的图                        |
|        | Graph(In)             | 从输入流读入一副图                                           |
| int    | V()                   | 顶点数                                                       |
| int    | E()                   | 边数                                                         |
| void   | addEdge(v int, w int) | 向图中添加一条边 v-w，无向有向都可以                         |
| []int  | adj(v int)            | 节点 v 相联通的所有步长为1的点，有向图为由 v 指出的边所连接的所有顶点 |
| string | toString()            | 对象的字符串表示                                             |

上面这个 api 的函数可以根据是有向图或者无向图而选择具体的不同实现。直接创建有向图无向图不同的结构体然后绑定不同的实现函数接口。

无向图代码：(有向图也差不多，只不过是插入边那个函数不一样了，但是注意有向图的入度与出度)

```go
package graph

import (
	"code/linklist"
)

// Graph 图的 API 接口
type Graph interface {
	V() int           // 图的顶点数
	E() int           // 图的边数
	AddEdge(int, int) // 向图中加入一条边
	adj(int) []int    // 和 v 相邻的所有顶点

}

// 图中链表的节点结构可以使用 linkList 包内的 LNode

// NDGraph 无向图
type NDGraph struct {
	vertex []*linklist.LNode // 切片，存放顶点
	edge   int               // 存放边的数目
}

// InitNDGraph 初始化一个图，初始化的时候要指定图的顶点数
func InitNDGraph(v int) *NDGraph {
	graph := new(NDGraph)
	graph.edge = v
	graph.vertex = make([]*linklist.LNode, v, v)
	for i := 0; i < v; i++ {
		graph.vertex[i] = linklist.NewLNode(i, nil)
	}
	return graph
}

// V 图的顶点数
func (graph *NDGraph) V() int {
	return len(graph.vertex)
}

// E 图的边数
func (graph *NDGraph) E() int {
	return graph.edge
}

// AddEdge 向图中加入一条边 v--w
func (graph *NDGraph) AddEdge(v int, w int) {
	nodev := linklist.NewLNode(v, nil)
	nodew := linklist.NewLNode(w, nil)
	// nodev 插入 w 行
	nodev.SetNext(graph.vertex[w].GetNext())
	graph.vertex[w].SetNext(nodev)
	// nodew 插入 v 行
	nodew.SetNext(graph.vertex[v].GetNext())
	graph.vertex[v].SetNext(nodew)
	graph.edge++
}

// adj 和 v 相邻的所有顶点
func (graph *NDGraph) adj(v int) []int {
	result := make([]int, 0, 5)
	position := graph.vertex[v].GetNext()
	var value int
	for position != nil {
		// 类型断言转换后
		value, _ = position.GetElement().(int)
		result = append(result, value)
		position = position.GetNext()
	}
	return result
}

// degree 计算某一节点的度数
func (graph *NDGraph) degree(v int) int {
	return len(graph.adj(v))
}

// maxDegree 计算所有节点里面最大的度数
func (graph *NDGraph) maxDegree(v int) int {
	max := 0
	for i := 0; i < len(graph.vertex); i++ {
		if graph.degree(i) > max {
			max = graph.degree(i)
		}
	}
	return max
}

// avgDegree 计算所有顶点的平均度数
func (graph *NDGraph) avgDegree(v int) float64 {
	return float64(2.0*graph.edge) / float64(len(graph.vertex))
}

// numberOfSelfLoops 计算自环的个数
func (graph *NDGraph) numberOfSelfLoops(v int) int {
	loops := 0
	for i := 0; i < len(graph.vertex); i++ {
		adj := graph.adj(i)
		for j := 0; j < len(adj); j++ {
			if i == adj[j] {
				loops++
			}
		}
	}
	return loops
}

```



### 5.2 图的遍历算法

#### 5.2.1 深度优先搜索（DFS）

##### 5.2.1.1 深度优先搜索

基本思想：是从起始顶点开始，沿着一条路一直走到底，如果发现不能到达目标解，那就返回到上一个节点，然后从另一条路开始走到底，这种尽量往深处走的概念即是深度优先的概念。
实现要诀：访问一个顶点并将其标记为访问，然后递归地访问所有与该顶点邻接且未标记的顶点，深度利用了可以下压的栈（递归支持）。

代码：

```go
// DepthFirstSearch 深度优先搜索（这儿用栈来做）
// 指定从 v  顶点开始深度优先搜索
func (graph *NDGraph) DepthFirstSearch(v int) {
	// 变量的预先声明，不要在循环体内，循环内声明的话费时
	var top interface{}
	var topvalue int
	var adj []int
	// 这是标识数组，表示这个下标对应的顶点是否被访问过
	isMarked := make([]bool, graph.V(), graph.V())
	// 可以使用栈来模拟递归
	stack := stack.InitAStack(graph.V())
	// 将图的顶点 v 放入栈内
	stack.Push(graph.vertex[v])
	// 对栈进行一个循环
	for !stack.IsEmpty() {
		// 弹出栈顶，获取栈顶元素
		// 打印输出元素或者执行别的操作
		top, _ = stack.Pop()
		topvalue, _ = top.(int)

		adj = graph.adj(topvalue)
		// 将其相连的但是没有访问过的节点放入栈内，并进行标记
		for i := 0; i < len(adj); i++ {
			if !isMarked[adj[i]] {
				stack.Push(adj[i])
				isMarked[i] = true
			}
		}

	}

}

// Dfs 深度优先搜索, 使用递归的方法来处理
// 指定从 v  顶点开始深度优先搜索
func (graph *NDGraph) Dfs(v int) {
	// 这是标识数组，表示这个下标对应的顶点是否被访问过
	isMarked := make([]bool, graph.V(), graph.V())
	dfs(graph, isMarked, v)
}

// dfs 递归体
func dfs(graph *NDGraph, isMarked []bool, v int) {
	isMarked[v] = true
	// 相邻顶点
	adj := graph.adj(v)
	for i := 0; i < len(adj); i++ {
		// 相邻顶点未被访问就访问
		if isMarked[adj[i]] {
			// 递归访问
			dfs(graph, isMarked, adj[i])
		}
	}
}
```



##### 5.2.1.2 深度优先搜索的应用

**查找路径：**

单点路径问题在图的处理领域十分重要，根据标准设计模式，可以使用以下 API 。

|       |                         |                                 |
| ----- | ----------------------- | ------------------------------- |
|       | (G *Graph) Paths(s int) | 在G中查找所有起点为 s 的路径    |
| bool  | hasPathTo(v int)        | 是否存在 s 到 v 的路径          |
| []int | pathTo(v int)           | s 到 v 的路径，如果不存在返回空 |

基于深度优先实现上述查找路径，需要对原有的深度优先代码进行扩展。添加一个整型数组edgeTo[]。这个数组可以找到每个与 s 连通的顶点回到 s 的路径，也就是这个路径上当前顶点的上一个节点。通过这个数组，会记住每个顶点到起点的路径而不是记录当前顶点到起点的路径。最后，edgeTo[] 会是一棵由父链接表示的数组。

<img src="img\寻找路径.jpg" alt="寻找路径" style="zoom:15%;" />

代码我就不写了，看书吧。

**连通分量**：

可以采取类似上面查找路径的思路，在edgeTo 的基础上再加一个数组用来存放连通分量的起点顶点和唯一标识符（可以是数组下标）。通过一个循环，存在节点未被访问的时候执行循环，循环内部从当前节点开始执行上面的查找路径并标记，此次循环内部调用深度优先递归函数所到达的顶点即是属于一个连通分量。然后将初始节点存入刚刚那个存放连通分量的起点顶点和唯一标识符的数组内。

**比较：**理论上来说，上述的算法比union-find算法快，但在实际应用的时候两个差别不大。而且union-find 是动态算法，不需要完整的构造一个图，所以一般来说union-find 是更好的选择。

**检测环：**



**双色问题：**



**拓扑排序：**



#### 5.2.2 广度优先搜索（BFS）

类似与树的层序遍历，广度优先搜索使用一个队列来保存所有已经被标记过但是其邻接表还未被检查过的节点。先将起点加入队列，然后重复以下步骤直到队列为空：

- 取队列的队首顶点 v ，并标记它；
- 将与顶点 v 相邻的所有未被标记过的顶点加入队列。

**单点最短路径**：那么，在使用广度优先搜索查找**单点最短路径**从时候，可以使用与深度优先里面类似的操作，使用一个edgeTo整数数组，在进行遍历的时候执行相关的操作，最后edgeTo会是一棵由父链接表示的数组。

代码：

```go
// Bfs 深度优先搜索, 指定从 v  顶点开始深度优先搜索
func (graph *NDGraph) Bfs(v int) {
	// 这是标识数组，表示这个下标对应的顶点是否被访问过
	isMarked := make([]bool, graph.V(), graph.V())

	// edgeTo := make([]int, graph.V(), graph.V())
	// 这儿使用一个队列来实现
	queue := queue.NewLLQueue(graph.V() + 1)

	// 首先，起点入队
	queue.EnQueuEe(graph.vertex[v])
	for !queue.IsEmpty() {
		// 出队
		first, _ := queue.DeQueue()
		firstValue, _ := first.(int)
		// 找到相邻的顶点
		adj := graph.adj(firstValue)
		for value := range adj {
			// 没被标记访问过
			if !isMarked[value] {
				// edgeTo[value] = firstValue
				// 标记一下，已经访问了
				isMarked[firstValue] = true
				queue.EnQueuEe(value)
			}
		}
	}
}
```

对于上述的，代码，把隐掉的 edgeTo相关代码释放出来就是查找路径的算法了，不过**此时的 edgeTo 也是一棵由父链接表示的数组，他表示 s 到每个与 s 连通的顶点的最短路径**。此时要获取从起点到某个点的最短路径，只要调用深度优先同样的 pathTo 函数即可。

#### 5.2.3 A*算法

https://blog.csdn.net/hitwhylz/article/details/23089415

### 5.3 图的最短路径算法

#### 5.3.1 Dijkstra算法



#### 5.3.2 Bellman-Ford算法



#### 5.3.3 Floyd-Warshall算法



### 5.4 图的最小生成树算法

#### 5.4.1 Prim算法

每次选择与已有树相连的最小权值的节点加入树中。

#### 5.4.2 Kruskal算法

在不会导致出现环的情况下每次选择最小权值的边加入，直到加入了 v -1 个边。

### 5.5 图匹配算法

#### 5.5.1 匈牙利算法



### 5.6 强连通分支算法与网络流

#### 5.6.1 Ford-Fulkerson算法



## 6、字符串

### 6.1 字符串排序

对于很多应用，决定排序顺序的键都是字符串，所以学习对字符串进行排序很有必要。

#### 6.1.1 键索引计数法

键索引计数法并不是直接的字符串排序算法，而是一个适用于小整数键的简单排序算法，这个算法是下面三个字符串排序算法的基础。

键索引计数法对于小整数键排序非常有效，但是常常被忽略。

假设有如下数组 a[]，数组中每个元素都保存着一个名字和组号，其中组号为小整数在 0 到 R-1 之间，我们要以组号为键进行分组排序。

<img src="img\键索引计数法1.jpg" alt="键索引计数法1" style="zoom:15%;" />

那么我们就可以：

1. **进行频率统计**

   设置一个大小为 R 的 int 数组（键的取值范围）count[]，数组每个元素的下标代表键值。遍历源数组 a[]，根据键值，在 count[] 对应的位置上 +1，表示有多少元素的键值等于当前下标。

2. **将频率转换为索引**

   接下来，使用 count[] 来计算每个键在排序结果数组中的索引位置。例如 count[] = [0，2，5，7，3]，那么在排序完成后的数组内，键值为0 的元素开始下标为0，键值为 1 的元素开始下标为 2， 键值为 2 的元素开始下标为 7 。即经过处理的 count[] 数组为：[0，2，7，14，17]。

3. **数据分类**

   完成上述步骤之后，将数组 a[] 所有元素拷贝到一个辅助数组中 aux[]，在拷贝的时候，a[] 中的每个元素在 aux[] 数组中的位置要根据 count[] 数组来。在对应的一个键值 i 根据 count[i-1] 放入 aux[] 中之后，对应的 count[i-1]++，表示下一个相同键值元素在 aux[] 数组中的位置。

4. **回写**

   最后，将 aux 数组回写至原始数组中。

5. **代码：**

   代码就不写了吧，这个不难啊。

#### 6.1.2 低位优先的字符串排序

低位优先的字符串排序相当于是**对键索引计数方法的一个扩展**，主要用于**处理固定长度字符串**，比如说手机号，固定电话，银行卡卡号，字符串的长度为N，从右向左开始进行每个键作为值开始遍历。

可以这样想，从右向左，先按右边第一个字符遍历然后排序（这里面是和上面的键索引计数法一样的），然后再看倒数第二个位置上的字符再次依照键索引计数法排序。就例如有 345 和 374 和 234 这三个字符串，第一次遍历后的顺序是 314，234，345，再对倒数第二个位置进行排序后是 314，234， 245，再对第一个字符排序就是 234，245，314。当然这面是用数字举例的，字符串也一样的，从后向前比较每个字符。

这个实现也简单，在原先键索引计数法外面加一层循环，从低位到高位即可。

#### 6.1.3 高位优先的字符串排序

高位优先字符串排序顾名思义就是要从高位开始进行排序。它的思路是：首先，使用键索引计数法将所有字符串按照首字母进行排序；然后，再将每个首字母对应的子数组排序（此时就忽略首字母，因为每一类中字符串的首字母都是一样的）。这里面实现起来就要注意了，因为字符串的长度可能不一样。在同一组内，进行划分的时候，有些已经处于字符串的末尾没有下一个字符，而有些还有。

这个实现看算法书上吧，理解和实现起来并不难。

#### 6.1.4 三向字符串快速排序

上面高位优先字符串排序是很优秀，但是在处理等值键或者有较长公共子序列的时候，会在递归的内部产生很多很多的子数组，而这些子数组有很多又是空的，所以就会产生很大的资源浪费，拖长运行时间。

为了解决这个问题，可以参考快速排序的做法。快排每次递归将元素分为三个部分，等于的部分，小于键值的部分和大于键值的部分。参考快排，就可以得出三向字符串排序：每次排序将数组根据首字母分为三个部分，首字母等于键值的部分、首字母小于键值的部分和首字母大于键值的部分，然后在这三个部分的子数组中再次进行递归操作。

在字符串很长或者公共部分较多的时候，三向字符串快速排序要比快速排序快很多。

代码我就不写了，看书吧。

### 6.2 单词查找树

单词查找树的数据结构是一种树型结构，它由字符串键中所有字符构造而成，允许使用被查找键中的字符进行查找。单词查找树以被查找的键中的字符为导向的。每个结点包含下一个可能出现的所有字符的链接，从根节点开始，首先经过的是键的首字母所对应的链接；在下一个结点中沿着第二个字符所对应的链接继续前进……如此这般知道最后一个结点或遇到一个空连接。

所以，单词查找树适用于较短键和较小的字母表，字母表太大的话内存占用会很大。树的结点结构如下：

```go
type Node struct {
    // 字符表是26个字母的话
    val byte // 这是这个结点的字符，byte也即一个uint8类型，代表了 ASCII 码的一个字符。
    child [26]*Node // 存储孩子的数组，长度为26
}
```

<img src="img\单词查找树.png" alt="单词查找树"  />

其实这样的树实用性不强，树的查找和插入不难的，那就这样吧。

### 6.3 子字符串查找

#### 6.3.1 暴力查找

有一个文本串S，和一个模式串P，现在要查找P在S中的位置，怎么查找呢？

  如果用暴力匹配的思路，并假设现在文本串S匹配到 i 位置，模式串P匹配到 j 位置，则有：

- 如果当前字符匹配成功（即S[i] == P[j]），则i++，j++，继续匹配下一个字符；
- 如果失配（即S[i]! = P[j]），令i = i - (j - 1)，j = 0。相当于每次匹配失败时，i 回溯，j 被置为0。

 举个例子，如果给定文本串S“BBC ABCDAB ABCDABCDABDE”，和模式串P“ABCDABD”，现在要拿模式串P去跟文本串S匹配，整个过程如下所示：

  *1.* S[0]为B，P[0]为A，不匹配，执行第②条指令：“如果失配（即S[i]! = P[j]），令i = i - (j - 1)，j = 0”，S[1]跟P[0]匹配，相当于模式串要往右移动一位（i=1，j=0）

![匹配1](img\匹配1.png)

*2*. S[1]跟P[0]还是不匹配，继续执行第②条指令：“如果失配（即S[i]! = P[j]），令i = i - (j - 1)，j = 0”，S[2]跟P[0]匹配（i=2，j=0），从而模式串不断的向右移动一位（不断的执行“令i = i - (j - 1)，j = 0”，i从2变到4，j一直为0）

![匹配2](img\匹配2.png)

 *3*. 直到S[4]跟P[0]匹配成功（i=4，j=0），此时按照上面的暴力匹配算法的思路，转而执行第①条指令：“如果当前字符匹配成功（即S[i] == P[j]），则i++，j++”，可得S[i]为S[5]，P[j]为P[1]，即接下来S[5]跟P[1]匹配（i=5，j=1）

![匹配3](img\匹配3.png)

*4*.S[5]跟P[1]匹配成功，继续执行第①条指令：“如果当前字符匹配成功（即S[i] == P[j]），则i++，j++”，得到S[6]跟P[2]匹配（i=6，j=2），如此进行下去

![匹配4](img\匹配4.png)

*5*. 直到S[10]为空格字符，P[6]为字符D（i=10，j=6），因为不匹配，重新执行第②条指令：“如果失配（即S[i]! = P[j]），令i = i - (j - 1)，j = 0”，相当于S[5]跟P[0]匹配（i=5，j=0）

![匹配5](img\匹配5.png)

*6*.至此，我们可以看到，如果按照暴力匹配算法的思路，尽管之前文本串和模式串已经分别匹配到了S[9]、P[5]，但因为S[10]跟P[6]不匹配，所以文本串回溯到S[5]，模式串回溯到P[0]，从而让S[5]跟P[0]匹配。

![匹配6](img\匹配6.png)

而S[5]肯定跟P[0]失配。为什么呢？因为在之前第4步匹配中，我们已经得知S[5] = P[1] = B，而P[0] = A，即P[1] != P[0]，故S[5]必定不等于P[0]，所以回溯过去必然会导致失配。那有没有一种算法，让i 不往回退，只需要移动j 即可呢？

答案是肯定的。这种算法就是KMP算法，它利用之前已经部分匹配这个有效信息，保持i 不回溯，通过修改j 的位置，让模式串尽量地移动到有效的位置。

#### 6.3.2 KMP(Knuth-Morris-Pratt)子字符串查找算法

涉及有限状态自动机的知识。

#### 6.3.3 BM(Boyer-Moore)字符串查找算法



#### 6.3.4 Rabin-Karp 指纹字符串查找算法



### 6.4 正则表达式



### 6.5 数据压缩





