<div align="center">

## POPULATING NODES OF A TREEVIEW ON DEMAND


</div>

### Description

This code allows to avoid the problem of a large amount of data to display in a TreeView.

Infact, using the population on demand property of this control you can choose to fill only parts of it (fill a node only when the user expands it).
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Erika Stival](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/erika-stival.md)
**Level**          |Beginner
**User Rating**    |4.7 (14 globes from 3 users)
**Compatibility**  |C\#, ASP\.NET
**Category**       |[Controls/ Forms/ Dialogs/ Menus](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/controls-forms-dialogs-menus__10-3.md)
**World**          |[\.Net \(C\#, VB\.net\)](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/net-c-vb-net.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/erika-stival-populating-nodes-of-a-treeview-on-demand__10-5092/archive/master.zip)





### Source Code

<strong>POPULATING NODES OF A TREEVIEW ON DEMAND:</strong><br>
<br>
Set the PopulateOnDemand property of the TreeView to true for any TreeNode that you want to fill in at the last minute.<br>
When the users expand the branch, the TreeView will fire a TreeNode populate event, which you can use to<br>
add the next level of nodes.<br>
Here is the code to do this.<br>
All you need is:<br>
A treeview control (id of the control: TreeView1)<br>
A SqlDatabase with one table: TCategories(CategoryID, Name, ParentCategoryID); the table is related to itself<br>
(CategoryID is connected to ParentCategoryID) to form a tree made by levels of parents and children categories.<br>
(the case with two related tables as TCategories(Name,ID) and TProducts(Name,ID), where TCategory contains the parents<br>
and TProducts the children is slightly different)<br>
<p>using System;<br>
 using System.Data;<br>
 using System.Configuration;<br>
 using System.Web;<br>
 using System.Web.Security;<br>
 using System.Web.UI;<br>
 using System.Web.UI.WebControls;<br>
 using System.Web.UI.WebControls.WebParts;<br>
 using System.Web.UI.HtmlControls;</p>
<p>public partial class _Default : System.Web.UI.Page<br>
 {<br>
 //variables useful for the code in the PageLoad<br>
 public System.Data.DataSet DataSetCategories;<br>
 public System.Data.SqlClient.SqlConnection ObjConn;<br>
 public System.Data.SqlClient.SqlCommand SqlCommandTreeView;<br>
 public System.Data.SqlClient.SqlDataAdapter myAdapter;<br>
 public System.String StrSQL;<br>
 //variables useful for the code in the TreeView1_TreeNodePopulate1 event<br>
 public System.Data.DataSet DataSetCategories1;<br>
 public System.Data.SqlClient.SqlConnection ObjConn1;<br>
 public System.Data.SqlClient.SqlCommand SqlCommandTreeView1;<br>
 public System.Data.SqlClient.SqlDataAdapter myAdapter1;<br>
 public System.String StrSQL1;<br>
 //variables useful to create the datareader<br>
 public System.Data.SqlClient.SqlConnection ObjConn2;<br>
 public System.Data.SqlClient.SqlCommand SqlCommandTreeView2;<br>
 public System.String StrSQL2;<br>
 public System.Data.SqlClient.SqlDataReader SqlDataReader2;<br>
</p>
<p> </p>
<p> protected void Page_Load(object sender, EventArgs e)<br>
 { <br>
 //with this code I populate the TreeView control with the first series of nodes (depth=0)<br>
 if (!Page.IsPostBack)<br>
 {</p>
<p> //connection<br>
 this.ObjConn = new System.Data.SqlClient.SqlConnection();<br>
 this.ObjConn.ConnectionString = " "; //use your connection string here<br>
 //command<br>
 this.SqlCommandTreeView = new System.Data.SqlClient.SqlCommand();<br>
 this.SqlCommandTreeView.Connection = this.ObjConn;<br>
 //sql<br>
 //you have to make a query of this kind<br>
 this.StrSQL = " SELECT CategoryID, Name, ParentCategoryID From TCategories WHERE (ParentCategoryID = 1)";<br>
 this.SqlCommandTreeView.CommandText = this.StrSQL;<br>
 //dataadapter<br>
 this.myAdapter = new System.Data.SqlClient.SqlDataAdapter();<br>
 this.myAdapter.SelectCommand = this.SqlCommandTreeView;<br>
 //dataset<br>
 this.DataSetCategories = new DataSet();<br>
 this.DataSetCategories.Tables.Add("TCategories");<br>
 this.myAdapter.Fill(this.DataSetCategories.Tables["TCategories"]);</p>
<p> <br>
 //create a datatable dtCategories<br>
 DataTable dtCategories = this.DataSetCategories.Tables["TCategories"];<br>
 //create a datarow rows<br>
 DataRow[] rows = dtCategories.Select("ParentCategoryID=" + 1);<br>
 <br>
 foreach (DataRow singlerow in dtCategories.Rows) //for each singlerow in the collection datarow rows... <br>
 {<br>
 //..you have to assigned a name and a value<br>
 TreeNode nodeCategory = new TreeNode(singlerow["Name"].ToString(), singlerow["CategoryID"].ToString());<br>
 //..you have to choose populate on demand true, because singlerow certainly has children<br>
 nodeCategory.PopulateOnDemand=true;<br>
 //..you have to collapse the node, at first<br>
 nodeCategory.Collapse();<br>
 //.w1.Nodes.Add(nodeCategory);<br>
 <br>
 }</p>
<p> this.DataBind();<br>
</p>
<p> }<br>
</p>
<p> }<br>
</p>
<p> </p>
<p> protected void TreeView1_TreeNodePopulate(object sender, TreeNodeEventArgs e)<br>
 {</p>
<p> //now, you have to fire TreeView1_TreeNodePopulate event and fill the TreeView with the second level of nodes (depth=1)<br>
 //connection<br>
 this.ObjConn1 = new System.Data.SqlClient.SqlConnection();<br>
 this.ObjConn1.ConnectionString = " "; <%--//use your connection string here<br>
 //command<br>
 this.SqlCommandTreeView1 = new System.Data.SqlClient.SqlCommand();<br>
 this.SqlCommandTreeView1.Connection = this.ObjConn1;<br>
//sql<br>
//you have to make a query like this (e.Node.Value is the id of the "selected" node)<br>
this.StrSQL1 = " SELECT CategoryID, Name, ParentCategoryID From TCategories WHERE (ParentCategoryID = "+e.Node.Value+")";<br>
 this.SqlCommandTreeView1.CommandText = this.StrSQL1;<br>
 //dataadapter<br>
 this.myAdapter1 = new System.Data.SqlClient.SqlDataAdapter();<br>
 this.myAdapter1.SelectCommand = this.SqlCommandTreeView1;<br>
 //dataset<br>
 this.DataSetCategories1 = new DataSet();<br>
 this.DataSetCategories1.Tables.Add("TCategories");<br>
 this.myAdapter1.Fill(this.DataSetCategories1.Tables["TCategories"]);<br>
</p>
<p> //create a datatable dtCategories1<br>
 DataTable dtCategories1 = this.DataSetCategories1.Tables["TCategories"];<br>
 //create a datarow rows<br>
 <br>
 <br>
 WARNING.: the TreeView1_SelectedNodeChanged event fires after TreeView1_TreeNodePopulate1 event<br>
 so TreeView1.SelectedNode.Value can't be use here<br>
 instead, you have to use, with almost the same meaning, e.Node.Value <br>
 <br>
 <br>
DataRow[] rows1 = dtCategories1.Select("ParentCategoryID=" + Int32.Parse(e.Node.Value));</p>
<p> foreach (DataRow singlerow1 in dtCategories1.Rows) //for each singlerow1 in the collection datarow rows1...<br>
 {<br>
 //..you have to assigned a name and a value<br>
TreeNode nodeCategory1 = new TreeNode(singlerow1["Name"].ToString(), singlerow1["CategoryID"].ToString());</p>
<p> //..you have to add children to the selected node of the TreeView<br>
 e.Node.ChildNodes.Add(nodeCategory1);<br>
 <br>
 <br>
</p>
<p> <br>
 <br>
 //now, you have to create a DataReader <br>
 //if the ChildNodes have children, you have to choose populate on demand true again<br>
 //connection<br>
 this.ObjConn2= new System.Data.SqlClient.SqlConnection();<br>
 this.ObjConn2.ConnectionString = " "; //use your connection string<br>
 //open connection<br>
 this.ObjConn2.Open();<br>
 //command<br>
 this.SqlCommandTreeView2 = new System.Data.SqlClient.SqlCommand();<br>
 this.SqlCommandTreeView2.Connection = this.ObjConn2;<br>
 //sql<br>
 //with this sql, I want to know if the single row has children<br>
 this.StrSQL2 = " SELECT CategoryID, Name, ParentCategoryID From TCategories WHERE (ParentCategoryID = " + singlerow1["CategoryID"] + ")"; <br>
 //command<br>
 this.SqlCommandTreeView2.CommandText = this.StrSQL2;<br>
 this.SqlCommandTreeView2.CommandType = CommandType.Text;<br>
 //datareader<br>
 this.SqlDataReader2 = this.SqlCommandTreeView2.ExecuteReader();<br>
 //if the datareader has rows and can be read...<br>
 if (this.SqlDataReader2.HasRows == true)<br>
 {<br>
 if (this.SqlDataReader2.Read())<br>
 {<br>
</p>
<p> //...the ChildNodes have children, so populate on demand true<br>
 nodeCategory1.PopulateOnDemand = true;<br>
 // at first, the node has to be collapsed<br>
 nodeCategory1.Collapse();<br>
 }<br>
</p>
<p> }<br>
 //close the connection<br>
 this.ObjConn2.Close();<br>
</p>
<p> <br>
</p>
<p> </p>
<p> }</p>
<p> this.DataBind();</p>
<p> </p>
<p> </p>
<p> }</p>
<p>}<br>
</p>

