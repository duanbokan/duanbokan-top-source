---

title: Android使用SQLite批量入库操作性能对比

date: 2015-10-16 14:28:28

tags: [Android , SQLite数据库 , 数据库性能]

categories: Android

keywords: Android , SQLite数据库 , 数据库性能

description: Android使用SQLite批量入库操作性能对比

top_img: http://image.jucaiwy.com/image/20151016/sqlite_2.jpg

cover: http://image.jucaiwy.com/image/20151016/sqlite_2.jpg

---


Android开发过程中经常会用到数据库，不可避免的会出现批量操作数据库，比如一次操作需要往数据库中插入一万条记录，这就需要进行批量操作，以下提供了四种批量操作方式，并分别进行对比。

- 直接使用SQL语句进行插入
- 直接使用SQL语句插入，添加事务
- 使用ContentValues方式
- 使用SQLiteStatement方式

接下来对每一种方式的实现及性能分别进行对比（向统一数据库同一张表插入一万条数据）：


### 1. 直接使用SQL语句进行插入

实现代码：

    @Override
    public CommonResult addGroupMember()
    {
        String insertSQL = "insert into "
                + SQLHelp.GROUP_MEMBER_TABLENAME
                + "("
                + SQLHelp.GROUP_ID
                + ","
                + SQLHelp.MEMBERS_SUBS_ID
                + ","
                + SQLHelp.MEMBER_NICK_NAME
                + ","
                + SQLHelp.MEMBER_ROLE
                + ","
                + SQLHelp.MEMBER_JID
                + ","
                + SQLHelp.MEM_UNION_KEY
                + ","
                + SQLHelp.CREAT_TIME
                + ", "
                + SQLHelp.UPDATE_TIME
                + ","
                + SQLHelp.ORDER_ID
                + ","
                + SQLHelp.EXT
                + " ) values(458906628,85469875,'xinwei',2,100856,18663,'2015-06-08','2015-06-09',123456,'beizhu')";
        SQLiteDatabase sqlDatabase = sqlHelp.getWritableDatabase();

        // 插入一万条
        for (int i = 0; i < 10000; i++)
        {
            sqlDatabase.execSQL(insertSQL);
        }

        return null;
    }


计时结果如下： 

![](http://image.jucaiwy.com/image/20151016/20151016135641277.png)

### 2. 直接使用SQL语句插入，添加事务

这种方法是在第一种方法的基础上，添加事务操作：

    // 开始
    sqlDatabase.beginTransaction();
    
    // 设置为成功
    sqlDatabase.setTransactionSuccessful();
    
    // 结束
    sqlDatabase.endTransaction();

在操作数据库之前设置为开始、数据库语句执行完毕后，设置数据库事务为Success，当执行到sqlDatabase.endTransaction()时，系统会判断之前是否有设置成功标志，如果没有则认为插入失败，数据库自动进行回滚操作。
 
执行代码如下：

	@Override
    public CommonResult addGroupMemberThing()
    {
        String insertSQL = "insert into "
                + SQLHelp.GROUP_MEMBER_TABLENAME
                + "("
                + SQLHelp.GROUP_ID
                + ","
                + SQLHelp.MEMBERS_SUBS_ID
                + ","
                + SQLHelp.MEMBER_NICK_NAME
                + ","
                + SQLHelp.MEMBER_ROLE
                + ","
                + SQLHelp.MEMBER_JID
                + ","
                + SQLHelp.MEM_UNION_KEY
                + ","
                + SQLHelp.CREAT_TIME
                + ", "
                + SQLHelp.UPDATE_TIME
                + ","
                + SQLHelp.ORDER_ID
                + ","
                + SQLHelp.EXT
                + " ) values(458906628,85469875,'xinwei',2,100856,18663,'2015-06-08','2015-06-09',123456,'beizhu')";

        SQLiteDatabase sqlDatabase = sqlHelp.getWritableDatabase();

        // 开始
        sqlDatabase.beginTransaction();

        // 循环一万次
        for (int i = 0; i < 10000; i++)
        {
            // 执行语句
            sqlDatabase.execSQL(insertSQL);
        }

        // 成功
        sqlDatabase.setTransactionSuccessful();

        // 结束
        sqlDatabase.endTransaction();

        return null;
    }


执行结束后，结果如下：

![](http://image.jucaiwy.com/image/20151016/20151016141825629.png)

### 3. 使用ContentValues方式

使用ContentValues的好处是可以动态拼接SQL语句进行执行，有更好的灵活性，可以直接使用对象进行赋值，代码如下：

	@Override
    public CommonResult addGroupMemberContentValues(CamTalkGroupMemberInfo memberInfo)
    {
        SQLiteDatabase sqlDatabase = sqlHelp.getWritableDatabase();
        try
        {
            sqlDatabase.beginTransaction();

            ContentValues values = new ContentValues();
            if (memberInfo != null)
            {
                values.put(SQLHelp.GROUP_ID, memberInfo.getGroupID());
                values.put(SQLHelp.MEMBERS_SUBS_ID, memberInfo.getMemberSubsID());
                values.put(SQLHelp.MEMBER_NICK_NAME, memberInfo.getMemberNickName());
                values.put(SQLHelp.MEMBER_ROLE, memberInfo.getMemberRole());
                values.put(SQLHelp.MEMBER_JID, memberInfo.getMemberJID());
                values.put(SQLHelp.MEM_UNION_KEY, memberInfo.getMemUnionKey());
                values.put(SQLHelp.CREAT_TIME, convertDate2String(memberInfo.getCreatTime()));
                values.put(SQLHelp.UPDATE_TIME, convertDate2String(memberInfo.getUpdateTime()));
                values.put(SQLHelp.ORDER_ID, memberInfo.getOrderID());
                values.put(SQLHelp.EXT, memberInfo.getExt());

                // 插入10000次
                for (int i = 0; i < 10000; i++)
                {
                    sqlDatabase.insert(SQLHelp.GROUP_MEMBER_TABLENAME, null, values);
                }
            }
            // 成功
            sqlDatabase.setTransactionSuccessful();
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
        finally
        {
            // 结束
            sqlDatabase.endTransaction();
            sqlDatabase.close();
        }

        return null;
    }

执行完毕后，结果如下：

![](http://image.jucaiwy.com/image/20151016/20151016142239726.png)

### 4. 使用SQLiteStatement方式

这个方法与上边ContentValues方式实现大致类似，不同的是需要自己编写SQL语句：

	@Override
    public CommonResult addGroupMemberStatement(CamTalkGroupMemberInfo memberInfo)
    {
        SQLiteDatabase sqlDatabase = null;

        String insertSQL = "insert into " + SQLHelp.GROUP_MEMBER_TABLENAME + "(" + SQLHelp.GROUP_ID
                + "," + SQLHelp.MEMBERS_SUBS_ID + "," + SQLHelp.MEMBER_NICK_NAME + ","
                + SQLHelp.MEMBER_ROLE + "," + SQLHelp.MEMBER_JID + "," + SQLHelp.MEM_UNION_KEY
                + "," + SQLHelp.CREAT_TIME + ", " + SQLHelp.UPDATE_TIME + "," + SQLHelp.ORDER_ID
                + "," + SQLHelp.EXT + ") values " + "(?,?,?,?,?,?,?,?,?,?)";

        try
        {
            sqlDatabase = sqlHelp.getWritableDatabase();

            // SQL事物控制-结束之前检测是否成功，没有成功则自动回滚
            sqlDatabase.beginTransaction();

            SQLiteStatement stat = sqlDatabase.compileStatement(insertSQL);
            stat.bindLong(1, memberInfo.getGroupID());
            stat.bindString(2, memberInfo.getMemberSubsID());
            stat.bindString(3, memberInfo.getMemberNickName());
            stat.bindLong(4, memberInfo.getMemberRole());
            stat.bindString(5, memberInfo.getMemberJID());
            stat.bindString(6, memberInfo.getMemUnionKey());
            stat.bindString(7, convertDate2String(memberInfo.getCreatTime()));
            stat.bindString(8, convertDate2String(memberInfo.getUpdateTime()));
            stat.bindString(9, memberInfo.getOrderID());
            stat.bindString(10, memberInfo.getExt());

            // 插入10000次
            for (int i = 0; i < 10000; i++)
            {
                stat.executeInsert();
            }

            // 成功
            sqlDatabase.setTransactionSuccessful();
        }
        catch (SQLException e)
        {
            e.printStackTrace();
        }
        finally
        {
            // 结束
            sqlDatabase.endTransaction();
            sqlDatabase.close();
        }
        return null;
    }

执行结果如下： 

![](http://image.jucaiwy.com/image/20151016/20151016142545977.png)

### 5. 总的对比图

![](http://image.jucaiwy.com/image/20151016/sqlite.png)

综上，对比四种结果，使用SQLiteStatement 插入最快，插入一万条数据，只需三秒钟，性能最好。