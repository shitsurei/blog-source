---
title: 策略模式
copyright: true
sticky: false
date: 2018-11-17 15:15:37
permalink: design2
categories: 设计模式
tags: [面向过程风格,简单工厂模式]
description: 设计模式学习笔记

---

日常设计过程中我们常常会遇到这样的需求，在得到一个数据结果的过程中，有时会选择这样或那样不同的处理过程，也就是说目的相同，算法却不同，并且这些算法之间还要频繁的替换，如果是在小型的程序中，还可以使用方法重载的技术实现这种需求，但是面对大型系统，这样基于函数级别的封装就显得不便于维护，每次增加新的算法就要使得原来已有的算法重新编译一边，代价较高，因此策略模型的引入就显得非常重要。

<!-- more -->

<div class="note info"><p>以下学习笔记内容由java语言编写。</p></div>

商场开业，需要在每台收银机上安装一个收银系统，要求每次录入商品的名称，单价和购买数量，点击确定按钮后就可以将当前商品的收费信息显示在收银界面，再次录入下一条商品信息点击确定后，将收费信息追加在收银界面，总价递增；商品录入结束后点击清零按钮刷新界面，清空录入列表和总价。

## 面向过程风格

<div class="note danger"><p>以下代码中包含java图形化编程相关接口调用，要读懂部分函数参数和功能需要一定图形化编程经验。</p></div>

```java
import java.awt.Font;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTextArea;
import javax.swing.JTextField;

public class CashView extends JFrame{
	private static final long serialVersionUID = 1L;
	//总价
	private double sum_price = 0.00;
	//构造方法即程序入口
	public CashView(){
		this.setTitle("商场收银系统");
		this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		//设置框架的宽高
		this.setSize(400, 450);
		//将中间容器放入框架
		this.add(getPanel());
		//设置框架不可缩放
		this.setResizable(false);
		//设置框架可见
		this.setVisible(true);
	}
	
	private JPanel getPanel() {
		JPanel panel = new JPanel();

        //设置布局为 null，绝对布局
        panel.setLayout(null);

        /* 这个方法定义了组件的位置。
         * setBounds(x, y, width, height)
         * x 和 y 指定左上角的新位置，由 width 和 height 指定新的大小。
         */
        // 创建商品名标签
        JLabel goodsLabel = new JLabel("商品:");
        goodsLabel.setBounds(20,20,50,30);
        goodsLabel.setFont(getCashFont(false));
        panel.add(goodsLabel);

        //创建商品名文本域
        JTextField goodsText = new JTextField(20);
        goodsText.setBounds(90,20,165,30);
        panel.add(goodsText);
        
        // 创建单价标签
        JLabel priceLabel = new JLabel("单价:");
        priceLabel.setBounds(20,60,50,30);
        priceLabel.setFont(getCashFont(false));
        panel.add(priceLabel);

        //创建单价文本域
        JTextField priceText = new JTextField(20);
        priceText.setBounds(90,60,165,30);
        panel.add(priceText);
        
        // 创建确定按钮
        JButton addButton = new JButton("确定");
        addButton.setBounds(280, 60, 80, 30);
        addButton.setFont(getCashFont(false));
        panel.add(addButton);
        
        // 创建数量标签
        JLabel numberLabel = new JLabel("数量:");
        numberLabel.setBounds(20,100,50,30);
        numberLabel.setFont(getCashFont(false));
        panel.add(numberLabel);

        //创建数量文本域
        JTextField numberText = new JTextField(20);
        numberText.setBounds(90,100,165,30);
        panel.add(numberText);
        
        // 创建清零按钮
        JButton resetButton = new JButton("清零");
        resetButton.setBounds(280, 100, 80, 30);
        resetButton.setFont(getCashFont(false));
        panel.add(resetButton);
        

        //创建文本域显示商品清单
        JTextArea jta= new JTextArea();
        //设置文本框不可编辑
        jta.setEditable(false);
        //在文本框上添加滚动条
        JScrollPane jsp = new JScrollPane(jta);
        //设置矩形大小.参数依次为(矩形左上角横坐标x,矩形左上角纵坐标y，矩形长度，矩形宽度)
        jsp.setBounds(20, 140, 340, 150);
        //默认的设置是超过文本框才会显示滚动条，以下设置让滚动条一直显示
        jsp.setVerticalScrollBarPolicy( JScrollPane.VERTICAL_SCROLLBAR_ALWAYS);        
        //把滚动条添加到容器里面
        panel.add(jsp);
        
        // 创建总价标签
        JLabel sumLabel = new JLabel("总计:");
        sumLabel.setBounds(20,300,50,30);
        sumLabel.setFont(getCashFont(false));
        panel.add(sumLabel);
        
        // 创建总价显示标签
        JLabel sumViewLabel = new JLabel(String.valueOf(sum_price));
        sumViewLabel.setBounds(100,300,250,60);
        sumViewLabel.setFont(getCashFont(true));
        panel.add(sumViewLabel);
        
        //确定按钮绑定事件（不加数值验证）
        addButton.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				if (goodsText.getText().equals("")||priceText.getText().equals("")||numberText.getText().equals("")) {
					JOptionPane.showMessageDialog(null,"内容不全");
				}else{
					//计算收费信息	
					int num = Integer.parseInt(numberText.getText());
					double price = Double.parseDouble(priceText.getText());
					jta.append("商品："+goodsText.getText()+" 单价："+price+" 数量："+num+" 合计："+num*price+"\n");
					sum_price+=num*price;
					sumViewLabel.setText(String.valueOf(sum_price));
				}
				
			}
		});
        
        //清零按钮绑定事件
        resetButton.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				goodsText.setText("");
				priceText.setText("");
				numberText.setText("");
				jta.setText("");
				sum_price=0d;
				sumViewLabel.setText(String.valueOf(sum_price));
			}
		});
        
        return panel;
        
    }
	
	private Font getCashFont(boolean is_big){
		Font f;
		if (is_big) {			
			f = new Font("黑体", Font.BOLD, 50); 
		}else{			
			f = new Font("黑体", Font.PLAIN, 20); 
		}
		return f;
	}
}

```
显而易见，这样的程序轻而易举的就能指出很多缺点：

> 展示界面的swing代码和用于计算的业务逻辑代码杂糅在一起，难以分别
> 需要修改原有业务逻辑时需要整个重新编译源程序
> 无法轻便高效的拓展原系统功能
> ...

以上代码虽然实现了收银系统的功能，但是面向过程的编程风格使得代码的可读性、可维护性、可扩展性都不强，面对小小的需求改变都难以适应。例如，商场开业为了酬宾推出部分商品折扣活动，有五折、八折、九折三种折扣分别适用于部分商品，在生成商品收费清单时，不仅要用所选的优惠方式进行计算，还要将折扣信息输出在收银界面上。
这是我们就应该考虑将业务逻辑封装起来，适应需求的变化。

## 业务逻辑封装

* 业务逻辑封装类

```java
/**
 * 折扣类封装
 * @author 张国荣
 *
 */
public class Discount {
	public static String[] reduce = {"正常收费","五折","八折","九折"};
	/**
	 * 根据商品原价和折扣类型计算商品收费价格
	 * @param money
	 * @param type
	 * @return
	 */
	public double calPrice(double money,String type){
		double discount;
		switch (type) {
		case "五折":
			discount=0.5;
			break;
		case "八折":
			discount=0.8;
			break;
		case "九折":
			discount=0.9;
			break;
		default:
			discount=1;
			break;
		}
		return money*discount;
	}
}
```

* 界面类

<div class="note primary"><p>以下代码省略相同部分。</p></div>

```java
……
        JComboBox<String> discountBox = new JComboBox<>();
        discountBox.setBounds(280, 20, 80, 30);
		//调用业务类中的静态常量来生成优惠选项
        for (String e : Discount.reduce) {		
        	discountBox.addItem(e);
		}
        panel.add(discountBox);
……
//确定按钮绑定事件（不加数值验证）
        addButton.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				if (goodsText.getText().equals("")||priceText.getText().equals("")||numberText.getText().equals("")) {
					JOptionPane.showMessageDialog(null,"内容不全");
				}else{					
					int num = Integer.parseInt(numberText.getText());
					double price = Double.parseDouble(priceText.getText());
					//通过传入优惠参数调用业务类中的静态方法计算商品费用
					double dis_price=Discount.calPrice(price*num, discountBox.getSelectedItem().toString());
					jta.append("商品："+goodsText.getText()+" 单价："+price+" 数量："+num+" "+discountBox.getSelectedItem()+" 合计："+dis_price+"\n");
					sum_price+=dis_price;
					sumViewLabel.setText(String.format("%.2f", sum_price));
				}
				
			}
		});
……
```

这个版本的代码相比第一次有了很大的改善，代码的重构使得展示界面的“前端”代码和真正用于处理数据的业务逻辑代码分开，降低了耦合度。在这个版本中，当我们要对系统的界面进行优化或者业务逻辑的需求出现变化时，我们可以很轻易在不同的类中做修改，并且完全不影响到另一个方面。
但是这样的代码就是好的设计吗，显示不是，我们依然可以从中挑出不足：

> 业务逻辑的代码全部放在一个类中，从这个角度耦合度并没有降下来
> 每一次进行业务逻辑的计算实际上只用到了类中的一个计算方法，但是其他计算方法也无可避免的参与了系统的编译和实例化
> 如果要加入新的业务功能，要么单独写类增加界面类的判断逻辑，提高界面和业务的耦合，要么融入到已有的业务类中，使得业务逻辑类的内部愈加复杂
> ……

基于上述的种种问题，我们想到在上一个章节中介绍了简单工厂模式，显然就可以运用到这个案例中来，业务逻辑实际上可以抽象出计算实际收费功能的父类和具体不同算法实现收费的子类，这样不同的计算方式就可以分开来，降低业务逻辑的耦合度。
再看来需求，商场为了回馈快开业期间消费者的支持特地推出了更加有力的优惠活动，满减活动，但是不与原有的折扣活动共享，消费者自主选择划算的优惠方式。

## 简单工厂模式实现

![design21](https://shitsurei-pictures.oss-cn-beijing.aliyuncs.com/pics/design21.jpg)

* 收费工厂类

```java
public class CashFactory {
	public static String[] reduce = {"正常收费","五折","八折","满三百减一百"};
	public static CashSuper reduce(String type){
		CashSuper cs = null;
		switch (type) {
		case "正常收费":
			cs = new CashNormal();
			break;
		case "五折":
			cs = new CashDiscount(0.5);
			break;
		case "八折":
			cs = new CashDiscount(0.8);
			break;
		case "满三百减一百":
			cs = new CashReturn(300, 100);
			break;
		}
		return cs;
	}
}
```

* 优惠父类

```java
public class CashSuper {
	public double calPrice(double price){
		return price;
	}
}
```

* 正常收费子类

```java
public class CashNormal extends CashSuper{
	public double calPrice(double price) {
		return price;
	}
}
```

* 折扣子类

```java
public class CashDiscount extends CashSuper{
	private double discount = 1d;
	public CashDiscount(double discount) {
		super();
		this.discount = discount;
	}	
	public double calPrice(double price) {
		return price*discount;
	}
}
```

* 满减子类

```java
public class CashReturn extends CashSuper{
	private double condition_money = 0d;
	private double return_money = 0d;
	public CashReturn(double condition_money, double return_money) {
		super();
		this.condition_money = condition_money;
		this.return_money = return_money;
	}
	public double calPrice(double price) {
		return price-((int)price/(int)condition_money)*return_money;
	}
}
```

* 界面类

```java
……
        // 创建折扣下拉框
        JComboBox<String> discountBox = new JComboBox<>();
        discountBox.setBounds(280, 20, 80, 30);
        //引入活动名称
        for (String e : CashFactory.reduce) {			
        	discountBox.addItem(e);
		}
        panel.add(discountBox);
……
		//确定按钮绑定事件（不加数值验证）
        addButton.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				if (goodsText.getText().equals("")||priceText.getText().equals("")||numberText.getText().equals("")) {
					JOptionPane.showMessageDialog(null,"内容不全");
				}else{			
					int num = Integer.parseInt(numberText.getText());
					double good_price = Double.parseDouble(priceText.getText());
					double goods_price = good_price * num;
					//根据选中的优惠方式从优惠工厂中生成优惠子类
					CashSuper cs = CashFactory.reduce(discountBox.getSelectedItem().toString());
					//调用优惠父类的抽象计算方法
					goods_price = cs.calPrice(goods_price);				
					//显示收费及活动信息
					sum_price+=goods_price;
					jta.append("商品："+goodsText.getText()+" 单价："+good_price+" 数量："+num+" "+discountBox.getSelectedItem()+" 合计："+goods_price+"\n");
					sumViewLabel.setText(String.format("%.2f", sum_price));
				}
			}
		});
……
```

到了这个版本，在业务逻辑方面的耦合度已经降得很低了，现在面对已有子类的新需求，我们只需要在工厂类中添加不同的参数名，返回相应的实例即可；而面对新的计算方法需求，添加计算方法类之后继承父类，并修改业务工厂中的调用即可。
到目前为止，虽然简单工厂模式也解决了收费算法的问题，但是这个模式只是解决了对象的创建问题，而且由于工厂本身包括了所有的收费方式，每次维护和扩展都要改动这个工厂，以致代码需要重新编译部署，所以这不是最好的办法。这里我们就要引出今天的策略模式了。

## 策略模式

> **策略模式（strategy）**定义了算法家族，分别封装起来，让其之间可以互相替换，使算法的变化不会影响到使用算法的用户

对于这个收银系统来说，不论是打折还是满减，甚至返利等算法，都是策略，用户真正想要得到的是优惠后所要付出的费用，而至于算法的实现过程并不关心。因此，我们真正所应该封装的，是不同的策略，而非类，这就是简单工厂模式所不能解决的问题，工厂只是解决了对象的创建，而用户所需要的只是类中的计算方法，而策略模式正好可以把对象也封装起来。在系统中，优惠父类即策略类，各种实现子类即具体策略类。

![design22](https://shitsurei-pictures.oss-cn-beijing.aliyuncs.com/pics/design22.jpg)

* 策略上下文

```java
public class CashContext {
	public static String[] reduce = {"正常收费","五折","八折","满三百减一百"};
	//将优惠父类封装到策略上下文中
	private CashSuper cs;
	public CashContext(String type) {
		switch (type) {
		case "正常收费":
			//每一次实例化都在上下文中实例优惠子类对象
			this.cs = new CashNormal();
			break;
		case "五折":
			this.cs = new CashDiscount(0.5);
			break;
		case "八折":
			this.cs = new CashDiscount(0.8);
			break;
		case "满三百减一百":
			this.cs = new CashReturn(300, 100);
			break;
		}
	}
	//通过上下文调用子类的计算方法
	public double calMoney(double money){
		return cs.calPrice(money);
	}
}
```

* 界面类

```java
……
        // 创建折扣下拉框
        discountBox = new JComboBox<>();
        discountBox.setBounds(280, 20, 80, 30);
        //引入活动名称
        for (String e : CashContext.reduce) {			
        	discountBox.addItem(e);
		}
        panel.add(discountBox);
……
        //确定按钮绑定事件（不加数值验证）
        addButton.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				if (goodsText.getText().equals("")||priceText.getText().equals("")||numberText.getText().equals("")) {
					JOptionPane.showMessageDialog(null,"内容不全");
				}else{			
					int num = Integer.parseInt(numberText.getText());
					double good_price = Double.parseDouble(priceText.getText());
					double goods_price = good_price * num;
					//将选中的优惠类型传入策略上下文
					CashContext cc = new CashContext(discountBox.getSelectedItem().toString());
					//调用策略上下文中的计算方法
					goods_price = cc.calMoney(goods_price);				
					//显示收费及活动信息
					sum_price+=goods_price;
					jta.append("商品："+goodsText.getText()+" 单价："+good_price+" 数量："+num+" "+discountBox.getSelectedItem()+" 合计："+goods_price+"\n");
					sumViewLabel.setText(String.format("%.2f", sum_price));
				}
			}
		});
……
```

实际上，这个版本的代码是策略模式和简单工厂模式的结合，将工厂类和策略模式的上下文结合了起来，使得代码更加轻便。对比策略模式和简单工厂模式的界面类代码，我们可以发现，简单工厂模式中我们的客户端需要使用工厂类和优惠父类两个类才可以完成策略的使用，而策略模式中只需要在客户端实例化策略上下文一个类就可以完成策略的使用，耦合度变得更低，连优惠父类也封装起来了。

## 总结

1. 策略模式定义了一系列算法的方法，这些算法所做的工作相同，只是实现不同。策略模式以相同的方法调用不同的算法，减少了算法类和使用算法类之间的耦合
2. 策略模式简化了单元测试，每个算法都有自己的类，可通过自己的接口单独测试
3. 实践中策略模式可以用来封装几乎任何类型的规则，只要在分析过程中听到需要在不同时间应用不同的业务规则，就可以考略使用策略模式处理这种变化的可能性

<hr />
