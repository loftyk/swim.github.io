# swim.github.io
Swim blog 
package com.swim.JiOuNum;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 *
 * 1~100奇数偶数交替打印
 * <P>.Tiltle: com.swim.JiOuNum</p>   .
 * <p> Description:</p>
 *
 * @author swim .
 * @date 2018/6/21 15:12
 */
public class TwoThread {

    private int start=1;
    /**
     * 保证内存可见性
     * 其实用锁了之后也可以保证可见性 这里用不用 volatile 都一样
     */
    private volatile  boolean flag=false;//先打印基数再打印偶数   false 打印基数 true打印偶数

    private final static Lock lock=new ReentrantLock();

    public static class  OuNum implements Runnable{
        private TwoThread number;

        public OuNum(TwoThread number) {
           this.number=number;
        }

        public void run() {
            while(number.start<=100){
                if (number.flag){
                    try {
                        lock.lock();
                        System.out.println(Thread.currentThread().getName()+"====>"+number.start);
                        number.start++;
                        number.flag=false;
                    }finally {
                        lock.unlock();
                    }
                }else{
                    try {
                        Thread.sleep(10);
                    }catch (InterruptedException e){
                        e.printStackTrace();
                    }
                }
            }
        }
    }

    public static  class JiNum implements Runnable{
        private TwoThread number;
        public JiNum(TwoThread number){
            this.number=number;
        }
        public void run() {
            while(number.start<=100){
                if (!number.flag){
                  try {
                      lock.lock();
                      System.out.println(Thread.currentThread().getName()+"====>"+number.start);
                      number.start++;
                      number.flag=true;
                  }finally {
                      lock.unlock();
                  }
                }else {
                    try {
                        Thread.sleep(10);
                    }catch (InterruptedException e){
                        e.printStackTrace();
                    }
                }
            }

        }
    }

    public static void main(String[] args){
        TwoThread twoThread=new TwoThread();
        Thread t1=new Thread(new JiNum(twoThread));
        Thread t2=new Thread(new OuNum(twoThread));
        t1.setName("基数线程a");
        t2.setName("偶数线程b");
        t1.start();
        t2.start();
    }
}
