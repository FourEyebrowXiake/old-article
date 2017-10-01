---
title: 理解Contentprovider
date: 2017-4-28 09:36:05
tags: Android
categories: 移动开发
---

## How to use content provider
### what is a content provider?
content provider是位于应用和其数据源之间地类。它能让应用轻松地访问相关数据源，并且访问过程得到管理。

### why is it useful?
1. 作为额外地抽象化操作，它允许开发者更改相关数据源，而不用更改访问内容提供器地应用中地代码。
2. SyncAdapters，Loaders，CursorAdapter需要通过contetn provider 访问数据。
3. 向其他应用开发数据源，使他们能够访问，使用修改你的数据源。

<!-- more -->

## 用途：
- 提取基础数据，安全高效地跨应用边界分享数据

- 轻松地切换数据源，而且更便于他人管理UI层代码

- 向小部件发送数据，或从更新的应用返回搜索结果

- 有效地与服务器同步，并在UI层实时更新

## ContentResolver
ContentResolver使应用与正确的ContentProvider沟通。

## Cursor
cursor是一种迭代器，使程序能够以表格的形式访问相关数据。

##### SimpleExample：
```java
// Get the TextView which will be populated with the Dictionary ContentProvider data.
        ListView dictListView = (ListView) findViewById(R.id.dictionary_list_view);

        // Get the ContentResolver which will send a message to the ContentProvider.
        ContentResolver resolver = getContentResolver();

        // Get a Cursor containing all of the rows in the Words table.
        Cursor cursor = resolver.query(UserDictionary.Words.CONTENT_URI, null, null, null, null);

        // Set the Adapter to fill the standard two_line_list_item layout with data from the Cursor.
        SimpleCursorAdapter adapter = new SimpleCursorAdapter(this,
                android.R.layout.two_line_list_item,
                cursor,
                COLUMNS_TO_BE_BOUND,
                LAYOUT_ITEMS_TO_FILL,
                0);

        // Attach the adapter to the ListView.
        dictListView.setAdapter(adapter);
```
