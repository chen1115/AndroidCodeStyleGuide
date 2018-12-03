
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fxtl8cge3dj32cr0lcacg.jpg)

## 前言

> 无论你是个人开发还是团队，一个良好的代码规范，能够在项目当中发挥举足轻重的作用；它不仅能使你们的开发更加高效，而且还会减少BUG产生的几率，增强代码可维护性及稳定性。

关于规范，我们分两部分来讲，因为Android主要是用Java语言来写的，所以我们区别对待。

## JAVA代码规范

#### 强制性规范:

1. 代码中的命名均不能以下划线或美元符号开始，也不能以下划线或美元符号结束。
2. 代码中的命名严禁使用拼音与英文混合的方式，更不允许直接使用中文的方式。
3. 类名使用UpperCamelCase 风格，必须遵从驼峰形式。
4. 方法名、参数名、成员变量、局部变量都统一使用 lowerCamelCase 风格，必须遵从驼峰形式。
5. 常量命名全部大写，单词间用下划线隔开，力求语义表达完整清楚，不要嫌名字长例如：**MAX_STOCK_COUNT**。
6. 抽象类命名使用 Abstract 或 Base 开头;异常类命名使用 Exception 结尾;测试类 命名以它要测试的类的名称开始。
7. 杜绝不规范的英文缩写：AbstractClass 缩写成AbsClass；condition缩写成condi;此类随意缩写严重降低了代码的可阅读性。
8. 如果使用到了设计模式，建议在类名中体现出具体的模式：
9. 关于Service或Dao层的命名
```
   public class ComponentFactory
   public class BufferStrategy
   public class ScrollerProxy
```
10. 实体类必须重载toString()方法，这样可以通过调用对象的toString()来排查问题。
11. Object 的 equals 方法容易抛空指针异常，应使用常量或确定有值的对象来调用 equals。
> 正例: "test".equals(object);
>  反例: object.equals("test");
12. 避免通过一个类的对象引用访问此类的静态变量或静态方法，无谓增加编译器解析成本，直接用类名来访问即可。

#### 推荐规范：
1. 集合初始化时，尽量指定集合初始值大小；
> ArrayList尽量使用ArrayList(int initialCapacity) 初始化 。

2. 使用 entrySet 遍历 Map 类集合 KV，而不是 keySet 方式进行遍历

> 说明:keySet 其实是遍历了 2 次，一次是转为 Iterator 对象，另一次是从 hashMap 中取出 key 所对应的 value。而 entrySet 只是遍历了一次就把 key 和 value 都放到了 entry 中，效 率更高。如果是 JDK8，使用 Map.foreach 方法。

3. 高度注意 Map 类集合 K/V 能不能存储 null 值的情况，如下表格:

| 集合类            | Key          | Value        | Super       | 说明       |
| ----------------- | ------------ | ------------ | ----------- | ---------- |
| Hashtable         | 不允许为null | 不允许为null | Dictionary  | 线程安全   |
| ConcurrentHashMap | 不允许为null | 不允许为null | AbstractMap | 分段锁技术 |
| TreeMap           | 不允许为null | 允许为null   | AbstractMap | 线程不安全 |
| HashMap           | 允许为null   | 允许为null   | AbstractMap | 线程不安全 |

4. 利用 Set 元素唯一的特性，可以快速对一个集合进行去重操作，避免使用 List 的 contains 方法进行遍历、对比、去重操作。
5. 通过双重检查锁(double-checked locking)(在并发场景)实现延迟初始化的优 化问题隐患(可参考 The "Double-Checked Locking is Broken" Declaration),推荐问题 解决方案中较为简单一种(适用于 JDK5 及以上版本)，将目标属性声明为 volatile 型。

```
*反例*：
class Foo {
    private Helper helper = null;
    
    public Helper getHelper() {
     if (helper == null) 
            synchronized(this) { 
                if (helper == null)
                    helper = new Helper(); 
            } 
            return helper; 
    } 
// other functions and members... 
} 
```

## Android代码规范

#### 代码：

1.  **Activity** 命名一律使用 **模块名+Activity** 的方式。例如，**LoginActivity、SignupActivity**；
2.  **Fragment** 命名一律使用 **模块名+Fragment** 的方式；
3. 自定义**View**：Custom(建议)+功能名+View/ViewGroup(具体的组件名称)。例如：**CustomImageScroller**、**CustomRatingBar**。
4.  **Widget** 小组件：**ScanWidget、WeatherWidget**。
5.  **Dialog**对话框：**功能名+Dialog**。例如：**LoginDialog、ProgressDialog** 
6. 尽量在每一个Activity或类中加入TAG,方便我们查看Activity的信息。(Tip : 使用Android Studio提供的快捷键**logt**可快速生成当前 类的常量)
7. 对于使用Intent传递数据，声明一些Key的时候：

> EXTRA_KEY_+具体Key名称，例如我们现在有一个人的名字和年龄要传那么首先定义：

```
public static final String EXTRA_KEY_PERSON_NAME="EXTRA_KEY_PERSON_NAME"
public static final String EXTRA_KEY_PERSON_AGE="EXTRA_KEY_PERSON_AGE"
```

然后在具体的页面 new Intent()，依次传递进去值，这样写其实没什么问题；但是试想一下，如果你要调用的Activity是类似于一个工具性质或通用的Activity（图片选择器、登录、注册等等），这时候你要传递的key又很多，如果业务复杂的话，你应该会被这样冗余且不易阅读的代码直接搞崩溃掉。

所以最好的办法就是在你要调用Activity提供一个**静态工厂方法**，要知道静态工厂方法所带来的好处太多了，由于Activity是不允许通过new的方式来初始化的，所以静态工厂方法的好处在此就不那么明显，但是已经足够我们优化我们的代码了。举个例子，我们有一个笔记 NoteActivity，用于创建笔记和修改笔记，

```
//笔记Id
private static final String EXTRA_KEY_NOTE_ID ="EXTRA_KEY_NOTE_ID" ;
//笔记内容
private static final String EXTRA_KEY_NOTE_CONTENT ="EXTRA_KEY_NOTE_CONTENT" ;
//笔记模式
private static final String EXTRA_KEY_NOTE_MODE ="EXTRA_KEY_NOTE_MODE" ;

//用于创建笔记
public static void startForCreate(Context context, int noteId) {
    start(context, noteId, null, MODE_CREATE);
}
    
//用于编辑笔记
public static void startForEdit(Context context, int noteId, String content) {
    start(context, noteId, content, MODE_UPDATE);
}

public static void start(Context context, int noteId, String content, int mode) {
    Intent starter = new Intent(context, TableShareListSettingActivity.class);
    starter.putExtra(EXTRA_KEY_NOTE_ID,noteId);
    starter.putExtra(EXTRA_KEY_NOTE_CONTENT,content);
    starter.putExtra(EXTRA_KEY_NOTE_CONTENT,mode);
    context.startActivity(starter);
}
```

通过以上方法，我们能够很好的解耦复杂的Activity之间的调用，再加上静态方法工厂方法名，代码可阅读行大大提高，最终我们看到的调用NoteActivity将会是很简洁的一段代码：

```
NoteActivity.startForCreate(this,noteId);
NoteActivity.startForEdit(this,noteId,content);
```

此外，Android Studio工具中其实已经在Live Template中提供了这样的代码:CMD+J( For MAC OS),简单的输入starter就可以快速地在当前的Activity中添加一个Intent的静态操作方法，这其实也说明了Android官方团队也鼓励我们这么做。
 如下图所示：

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fxtla83leij30la05lt97.jpg)



一下子省了好多代码，简直太赞了有木有！

8. 增加类注释，使用Android Studio的 File And Code Template：

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fxtlam313hj30ul0kb42i.jpg)



![](https://ws4.sinaimg.cn/large/006tNbRwgy1fxtlb01lizj30ul0kb41c.jpg)



1. 所有的常量加上注释，且功能相同的排放在一起，不同的进行换行；
2. Activity中变量采用m开头+类名。例如，mTable、mPerson；
3. Activity中的控件：m+模块名+控件类型名称。例如，**mLoginEditText,mLoginTextView**;

#### 资源Res

1.按照资源的类型，分为以下几种

> 控件Id命名：控件缩写 _模块（module) _功能名(function)

| 控件类型       | ID命名规则            |
| -------------- | --------------------- |
| TextView       | tv_module_function    |
| EditText       | et_module_function    |
| ImageView      | iv_module_function    |
| Button         | btn_module_function   |
| ListView       | lv_module_function    |
| GridView       | gv_module_function    |
| CheckBox       | check_module_function |
| RadioButton    | radio_module_function |
| LinearLayout   | ll_module_function    |
| RelativeLayout | rl_module_function    |
| FrameLayout    | fl_module_function    |
| GridLayout     | gl_module_function    |
| ···            | ···                   |

> Color资源命名

| Resources Type | 命名规则                                  |
| -------------- | ----------------------------------------- |
| color          | 组件名+具体作用名。例 R.color.button_text |

> String资源命名

| Resources Type | 命名规则                     |
| -------------- | ---------------------------- |
| string         | 具体功能。 例 R.string.hello |

> Drawable资源命名

| Resources Type | 命名规则                                                     |
| -------------- | ------------------------------------------------------------ |
| launcher icon  | ic_launcher。例R.drawable.ic_launcher                        |
| normal icon    | ic_具体模块_功能。例R.drawable.ic_audio_pause                |
| Toolbar icon   | ic_ab_功能名。例如ic_ab_search                               |
| selector       | selector_模块_功能名。例如 selector_login_button             |
| shape          | shape_模块*功能名*状态。例如 R.drawable.shape_login_button_normal |

> Layout资源命名

| 类型        | 命名规则                                                     |
| ----------- | ------------------------------------------------------------ |
| activity    | activity_模块名。例如 R.layout.activity_login                |
| fragment    | fragment_模块名。例如 R.layout.fragment_login_layout_header  |
| include     | layout_模块名_功能名。例如 @layout/layout_login_bottom       |
| adapter     | adapter_item_模块名_功能名。例如 R.layout.adapter_item_simple_text |
| dialog      | dialog_模块_功能名。例如 R.layout.dialog_time_picker         |
| list header | header_模块_功能。例如 R.layout.header_main_top_ad           |
| list footer | footer_模块_功能。例如 R.layout.footer_main_bottom_action    |
| widget      | widget_模块_功能。例如 R.layout.widget_app_clock             |
| ···         | ···                                                          |

> Menu资源命名

| Resources Type | 命名规则                     |
| -------------- | ---------------------------- |
| menu           | menu_模块名。例如 menu_login |

> Values资源命名

| Resources Type | 命名规则                                   |
| -------------- | ------------------------------------------ |
| color          | 模块名_color。例如 material_design_color   |
| dimens         | 模块名_dimens。例如 material_design_dimens |
| style          | 模块名_style。例如 material_design_style   |
| themes         | 模块名_themes。例如 material_design_themes |

## 总结

其实代码规范只是一个Guideline，没有说非要某一种风格来编写代码。如果你的团队有自己的一套代码规范，然后开发也很高效，代码也很容易阅读且可维护，就完全可以按照自己的团队的技术规范来。我想表达的是，在编写的代码的时候，能有一个Guideline（准则）或者说是一个约定，我们共同遵守这样的约定，来达到我一开始说的代码规范性所带来的意义。

> 正所谓，“离娄之明，公输子之巧，不以规矩，不能成方圆。”

## 参考资料

[阿里巴巴Java开发手册](https://github.com/zhangmiaocc/Android-/blob/master/%E9%98%BF%E9%87%8C%E5%B7%B4%E5%B7%B4Java%E5%BC%80%E5%8F%91%E6%89%8B%E5%86%8Cv1.2.0.pdf)
