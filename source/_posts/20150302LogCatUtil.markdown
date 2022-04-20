---

title: LogcatUtil

date: 2015-03-02 17:27:06

tags: [Android , LogcatUtil ]

categories: Android 

keywords: Android , LogcatUtil

description: LogcatUtil

top_img: http://image.jucaiwy.com/image/20150302/logcat.jpg

cover: http://image.jucaiwy.com/image/20150302/logcat.jpg

---


之前项目中，需要自己打印log，于是模仿别人的工具，整理了一个日志打印工具，支持开关、打印文件名、方法名、行数等：


	package com.xinwei.palmhall.util;  
  
	import java.text.SimpleDateFormat;  
	import java.util.Date;  
  
	import android.util.Log;  
  
	public class LogCatUtil {  
    private static final boolean DEBUG = true;  
  
    public static void d(String TAG, String method, String msg) {  
        if (DEBUG) {  
            Log.d(TAG, "[" + method + "]" + msg);  
        }  
    }  
  
    public static void d(String TAG, String msg) {  
        if (DEBUG) {  
            Log.d(TAG, "[" + getFileLineMethod() + "]" + msg);  
        }  
    }  
  
    public static void e(String TAG, String method, String msg) {  
        if (DEBUG) {  
            Log.e(TAG, "[" + method + "]" + msg);  
        }  
    }  
  
    public static void e(String TAG, String msg) {  
        if (DEBUG) {  
            Log.e(TAG, "[" + getFileLineMethod() + "]" + msg);  
        }  
    }  
  
    public static void i(String TAG, String method, String msg) {  
        if (DEBUG) {  
            Log.i(TAG, "[" + method + "]" + msg);  
        }  
    }  
  
    public static void i(String TAG, String msg) {  
        if (DEBUG) {  
            Log.i(TAG, "[" + getFileLineMethod() + "]" + msg);  
        }  
    }  
  
    public static void w(String TAG, String method, String msg) {  
        if (DEBUG) {  
            Log.w(TAG, "[" + method + "]" + msg);  
        }  
    }  
  
    public static void w(String TAG, String msg) {  
        if (DEBUG) {  
            Log.w(TAG, "[" + getFileLineMethod() + "]" + msg);  
        }  
    }  
  
    // 获取文件、行数  
    private static String getFileLineMethod() {  
        StackTraceElement traceElement = ((new Exception()).getStackTrace())[2];  
        StringBuffer toStringBuffer = new StringBuffer("[")  
                .append(traceElement.getFileName()).append(" | ")  
                .append(traceElement.getLineNumber()).append(" | ")  
                .append(traceElement.getMethodName()).append("]");  
        return toStringBuffer.toString();  
    }  
  
    // 获取行数  
    public static String getLineMethod() {  
        StackTraceElement traceElement = ((new Exception()).getStackTrace())[2];  
        StringBuffer toStringBuffer = new StringBuffer("[")  
                .append(traceElement.getLineNumber()).append(" | ")  
                .append(traceElement.getMethodName()).append("]");  
        return toStringBuffer.toString();  
    }  
  
    // 获取文件名  
    public static String _FILE_() {  
        StackTraceElement traceElement = ((new Exception()).getStackTrace())[2];  
        return traceElement.getFileName();  
    }  
  
    // 获取方法名  
    public static String _FUNC_() {  
        StackTraceElement traceElement = ((new Exception()).getStackTrace())[1];  
        return traceElement.getMethodName();  
    }  
  
    // 获取行数  
    public static int _LINE_() {  
        StackTraceElement traceElement = ((new Exception()).getStackTrace())[1];  
        return traceElement.getLineNumber();  
    }  
  
    // 获取时间  
    public static String _TIME_() {  
        Date now = new Date();  
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");  
        return sdf.format(now);  
    }  
	}  