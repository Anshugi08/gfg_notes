package com.java.oms.dao;

import java.util.ArrayList;
import java.util.List;

import com.java.oms.model.OrderInfo;

public class OrderDaoImpl implements OrderDao{
	static List<OrderInfo> orderList;
	private static int orderId=1;
	private static double billAmount;
	
	static {
		orderList = new ArrayList<OrderInfo>();
	}

	@Override
	public String placeOrder(OrderInfo order) {
		order.setOrderId(orderId++);
		billAmount = order.getPrice()*order.getQuantity();
		order.setBillAmount(billAmount);
		orderList.add(order);
		
		return "Order placed Successfully";
	}

	@Override
	public List<OrderInfo> showOrder(int custId) {
		List<OrderInfo> result = new ArrayList<>();

	    for (OrderInfo order : orderList) {
	        if (order.getCustomerId() == custId) {
	            result.add(order);
	        }
	    }
	    
	    return result;
	    
	}

	@Override
	public OrderInfo searchOrder(int custId) {
		OrderInfo found = null;
		for (OrderInfo orderInfo : orderList) {
			if(orderInfo.getCustomerId() == custId) {
				found = orderInfo;
				break;
			}
		}
		
		return found;
	}

	@Override
	public String editOrder(OrderInfo order) {
		OrderInfo found = searchOrder(order.getCustomerId());
		
		if(found != null) {
			found.setItemName(order.getItemName());
			found.setOrderDate(order.getOrderDate());
			found.setMenuId(order.getMenuId());
			found.setPrice(order.getPrice());
			found.setQuantity(order.getQuantity());
			billAmount = order.getPrice()*order.getQuantity();
			found.setBillAmount(billAmount);
			found.setOrderComments(order.getOrderComments());
		}
		
		return "Order list updated";
	}

	@Override
	public String removeOrder(int custId) {
		OrderInfo found = searchOrder(custId);
		
		if(found != null ) {
			orderList.remove(found);
			return "Order removed";
		}
		return "Order not removed";
	}
  
  
  
  
  
  
  
  
	package com.java.oms.bal;

import java.util.Calendar;
import java.util.Date;
import java.util.List;

import com.java.oms.Exception.OrderException;
import com.java.oms.dao.OrderDao;
import com.java.oms.dao.OrderDaoImpl;
import com.java.oms.model.OrderInfo;

public class OrderBal {
	
	static StringBuilder sb;
	static OrderDao orderDao;
	
	static {
		sb = new StringBuilder();
		orderDao = new OrderDaoImpl();
	}
	
	
	public static String placeOrderBal(OrderInfo order) throws OrderException {
		if(validateOrder(order)) {
			return orderDao.placeOrder(order);
		}
		throw new OrderException(sb.toString());
	}
	
	public static List<OrderInfo> showOrderBal(int customerId){
		return orderDao.showOrder(customerId);
	}
	
	public static OrderInfo searchOrderBal(int custId) {
		return orderDao.searchOrder(custId);
	}
	
	public static String editOrderBal(OrderInfo orderInfo) throws OrderException {
		if(validateOrder(orderInfo) == true) {
			return orderDao.editOrder(orderInfo);
		}
		throw new OrderException(sb.toString());
	}
	
	public static String removeOrderBal(int custId) {
		return orderDao.removeOrder(custId);
	}
	
	public static boolean validateOrder(OrderInfo order) {
		boolean isValid = true;
		
		Calendar calendar = Calendar.getInstance();
        calendar.setTime(new Date());
        calendar.set(Calendar.HOUR_OF_DAY, 0);
        calendar.set(Calendar.MINUTE, 0);
        calendar.set(Calendar.SECOND, 0);
        calendar.set(Calendar.MILLISECOND, 0);
        Date today = calendar.getTime();
        
        if(order.getOrderDate().before(today)) {
        	sb.append("You have entered yesterday's date.");
        	isValid = false;
        }
        
        return isValid;
        
        
	}
	
}





static Scanner sc;
	static OrderBal orderBal;
  
  
  static {
		sc = new Scanner(System.in);
		orderBal = new OrderBal();
      }
      
      public static void placeOrderMain() throws OrderException {
		OrderInfo order = new OrderInfo();
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
		
		System.out.println("Enter CustomerId ");
		order.setCustomerId(sc.nextInt());
		
		System.out.println("Enter MenuId ");
	    int menuId = sc.nextInt();
	    order.setMenuId(menuId);
      
      
      System.out.println("Enter Date(yyyy-MM-dd) ");
		try {
			Date date = sdf.parse(sc.next());
			order.setOrderDate(date);
		} catch (ParseException e) {
			// TODO Auto-generated catch block
			System.err.println(e.getMessage());
		}
		
		System.out.println("Enter Quantity ");
		order.setQuantity(sc.nextInt());	
		
		System.out.println("Give Some Comments ");
		order.setOrderComments(sc.next());
		
		System.out.println(orderBal.placeOrderBal(order));
		
	}
	
	public static void showOrderMain() {
		System.out.println("Enter CustomerId ");
		int custId = sc.nextInt();
		List<OrderInfo> order = orderBal.showOrderBal(custId);
		
		for (OrderInfo orderInfo : order) {
			System.out.println(orderInfo);
		}
		
	}
	
	public static void searchOrderMain() {
		System.out.println("Enter CustomerId ");
		int custId = sc.nextInt();
		OrderInfo found = orderBal.searchOrderBal(custId);
		
		if(found != null) {
			System.out.println(found);
		}
		else {
			System.out.println("Record not found");
		}
	}
  
  
  public static void editOrderMain() throws OrderException {
		OrderInfo order = new OrderInfo();
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
		
		System.out.println("Enter Customer ID");
	    order.setCustomerId(sc.nextInt());
		
	    System.out.println("Enter MenuId ");
	    int menuId = sc.nextInt();
	    order.setMenuId(menuId);
      
      
      System.out.println("Enter Date(yyyy-MM-dd) ");
		try {
			Date date = sdf.parse(sc.next());
			order.setOrderDate(date);
		} catch (ParseException e) {
			// TODO Auto-generated catch block
			System.err.println(e.getMessage());
		}
		
		System.out.println("Enter Quantity ");
		order.setQuantity(sc.nextInt());	
		
		System.out.println("Give Some Comments ");
		order.setOrderComments(sc.next());
		
		System.out.println(orderBal.editOrderBal(order));
		
	}
	
	public static void removeOrderMain() {
		System.out.println("Enter customer id");
		int custId = sc.nextInt();
		System.out.println(orderBal.removeOrderBal(custId));
	}
	
	public static void main(String[] args) {
		int choice;
    do {
    
    System.out.println("1. Place Order ");
            System.out.println("2. Show Customer Orders");
            System.out.println("3. Search Order");
            System.out.println("4. Edit Order");
            System.out.println("5. Delete Order");
            System.out.println("6. Exit");
            System.out.print("Choose an option: ");
            choice = sc.nextInt();
            sc.nextLine();
            
            switch(choice) {
            	case 1:
            		
            		try {
            			placeOrderMain();
            		} catch (OrderException e) {
            			// TODO Auto-generated catch block
            			System.err.println(e.getMessage());
            		}
            		break;
				
            	case 2:
            		showOrderMain();
            		break;
            		
            	case 3:
            		searchOrderMain();
            		break;
            		
            	case 4:
            		try {
            			editOrderMain();
            		} catch (OrderException e) {
            			// TODO Auto-generated catch block
            			e.printStackTrace();
            		}
            		break;
            		
            	case 5:
            		removeOrderMain();
            		break;
            		
            	case 6:
            		return;
            	
            }
			
		}
		while(choice != 6);
	}
}

	
}
