---
layout: post
title: UITableView编辑模式下，用原生方法实现移动排序实现，隐藏删除按钮，修改默认排序图片
categories: [blog]
tags: [Objective-c,UITableView]
description: UITableView编辑模式下，用原生方法实现移动排序实现
---

##### 需求及实现思路
 需求：对商品分类进行拖动排序。
 UITableView本身有delegate & dataSource方法可以进行选中cell进行排序，但调用`   [self.tableView setEditing:YES
                          animated:YES];`这个方法后，就会显示删除按钮&排序按钮，并且cell上的view整体向右平移，这就影响了原来想要的效果。刚开始也没想过去去修改或者调用系统方式达到理想效果，所以就找网上一些别人实现的效果来用，例如 [XRDragTableView
](https://github.com/codingZero/XRDragTableView),但在用的过程中发现并不是自己需要的效果（点击排序按钮，进行排序）但这个效果是长按cell,进行拖动排序。所以自己就花费了一点时间来研究UITableView编辑模式下的排序效果。

##### 实现效果
[点此链接跳转Demo](https://github.com/zfx5130/TableViewEditDemo)

##### 主要的几个实现方法

```
1.设置为NO以后，当编辑模式时，位置不移动。
- (BOOL)tableView:(UITableView *)tableView shouldIndentWhileEditingRowAtIndexPath:(NSIndexPath *)indexPath {
    return NO;
}
2.这个方法作用是隐藏编辑模式下的删除或者添加按钮。
- (UITableViewCellEditingStyle)tableView:(UITableView *)tableView
           editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath {
    return UITableViewCellEditingStyleNone;
}

```

##### 修改编辑模式下Reorder图标

```
//在cell中调用此方法即可替换。
- (void)setEditing:(BOOL)editing animated:(BOOL)animated {
    [super setEditing:editing animated:YES];
    if (editing) {
        for (UIView * view in self.subviews) {
            if ([NSStringFromClass([view class]) rangeOfString: @"Reorder"].location != NSNotFound) {
                for (UIView * subview in view.subviews) {
                    if ([subview isKindOfClass: [UIImageView class]]) {
                        CGRect frame = subview.frame;
                        frame.size.width += 10;
                        frame.size.height += 15;
                        subview.frame = frame;
                        ((UIImageView *)subview).image = [UIImage imageNamed: @"edit_image"];
                    }
                }
            }
        }
    }
}

```

                          


