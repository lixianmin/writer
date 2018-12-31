

-----

#### 二分查找



时间复杂度O(logN)
```c++
template<typename T>
int binSearch (T data[], int left, int right, T key)
{
	int i = left - 1;				// 不变式：data[i]<key
	int j = right + 1;				// 不变式：data[j]≥key
	while (i + 1 != j)				// 可以证明：当j≥i+2时[i,j]的范围是在不断缩小的
	{
		const int mid = i + (j-i)/2;// (i+j)/2当数字特别大时有可能溢出
		if (data[mid] < key)
        {
            i = mid;
        }
		else 
        {
            j = mid;
        }
	}
    
    // 查找不成功时如果需要将其插入的话，则无论j==right+1还是data[j]!=key，实际上j实际指向插入的位置
	if (j == right + 1 || data[j] != key)
    {
        return ~j;
    }
    
    // 查找成功时，j指向key在data[]中第一次出现的位置
	return j;
}
```



下面这个版本更加容易记忆，但效率较低，且不具备上个版本中j的特殊意义：

```c++
template<typename T>
int binSearch(T data[], int left, int right, T key)
{
	while(left<=right)			//不能用left<right，否则也许第一次就进不去
	{
		const int mid=i + (j-i)/2;	// (i+j)/2当数字特别大时有可能溢出
		if(data[mid]<key)
			left=mid+1;			//确保[left, right]在缩小
		else if(data[mid]>key)
			right=mid-1;
		else
			return mid;
	}
	return -1;
}
```
