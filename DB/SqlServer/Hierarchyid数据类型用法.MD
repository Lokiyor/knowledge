## 基本用法
* 返回子节点的第n个父节点
 > child.GetAncestor(n)  

* 返回一个节点在树中的深度的整数
 > node.GetLevel( )

* 返回层次结构树的根节点（静态方法)
> hierarchyid::GetRoot( )

* 判断子节点是否为父节点的后代    (@NodeId.IsDescendantOf(NodeId) = 1 返回@NodeId的所有父级  NodeId.IsDescendantOf(@NodeId) = 1 返回@NodeId的所有所有子集)
> child.IsDescendantOf(parent)

* 返回一个节点，其相对于根的路径是指向newRoot的路径，后跟从oldRoot 到当前的路径
> node.GetReparentedValue(oldRoot,newRoot)

* 将 hierarchyid 的规范字符串表示形式转换为 hierarchyid 值（静态方法）
> hierarchyid::Parse( )

* 返回具有路径表示形式的字符串(作用与Parse相反)
> node.ToString( )

