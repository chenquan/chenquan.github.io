---
layout: post
title: NSTestAttachment
categories: [blog]
tags: [NSTestAttachment]
header-img: "img/home-bg.jpg"
description:  NSTestAttachment
---

##### NSTextAttachment

`NSTextAttachment`对象一般与`NSAttributedString`一起搭配使用，一个`NSTextAttachment`可以包含`NSData`,` UIImage` 或者`NSFileWrapper`对象，我们通过修改属性来改变数据显示时候的外观。例如。我们需要在文字后面添加图片，这时候，我们就需要用到`NSTextAttachment`。具体实现如下

  ```

  -(void)testNSTextAttachment {
    NSTextAttachment *attchment = [[NSTextAttachment alloc] init];
    attchment.image = [UIImage imageNamed:@"test"];
    //bounds属性能改变图片的位置。
    attchment.bounds = CGRectMake(0, 0, 15, 15);
   
    //这里通过appendAttributedString 添加的先后图字的先后顺序，则是展示的前后顺序。
    NSMutableAttributedString *attachmentString = [[NSMutableAttributedString alloc] initWithAttributedString:[NSMutableAttributedString attributedStringWithAttachment:attchment]];

    [attachmentString appendAttributedString:[[NSAttributedString alloc] initWithString:@"测试测试测试测试测试试"]];
    self.testLabel.attributedText = attachmentString;
}   
  ```

[Demo](https://github.com/zfx5130/NSTextAttachmentTestDemo)

#####  
