---
layout: post
title: "Emacs orgģʽ�ļ�GTD����"
---

Emacs orgģʽ�ļ�GTD����
===============================

## intro
���ת��ʹ��emacs����Ȼ����д����Ҳ�м������ڵ�ʱ�䣬����emacs�ĺܶ๦�ܶ���û��������������Ҫ���Ч�ʣ����Գ���ʹ��orgģʽ��todo��agenda������GTD�Ĺ���

## ͬ��
��Ϊ�Ͼ�û���Ʊʼǻ�������GTD���һ����ͬ�����ܣ����Բ��ý�ɽ������Ϊͬ�����ߡ������ļ���ӵ�agenda�ļ��б�

    (setq org-agenda-files (list "e:/����/GTD/task.org"))

�����ӣ�ֻҪ����org-agenda�������Զ�������ӽ������ļ�������org-agenda-view��Ч���ǳ�Ư����

## TODO
���TODO�ǳ��򵥣�����ʹ��orgģʽ�ķֲ�ṹ�������������TODO��

    * Collection
    ** TODO Wash clothes

��TODO���ϣ������кܶ�TODO��ص��������ִ��

    C-c C-t     �л�TODO״̬��ѭ��ѡ��
    C-u C-c C-t �л�TODO״̬���ֹ�ѡ��
    C-c C-c     ���Tag
    C-c C-s     ���Schedule
    C-c C-d     ���Deadline
    C-c .       ���ʱ���
    C-u C-c .   ���׼ȷʱ���ʱ���
    C-c C-w     �ƶ�����ͬ���ڵ�

��TODO���ϣ�����[%]��������ʾ�ӽڵ�����״̬,������Ŀ����ǳ�����

TODO״̬����ͨ�������������ã������Ҹ����Ҹ�����Ҫ�����˷��ࣺ

    (setq org-todo-keywords '((type "TODO" "STARTED" "WAITING" "|" "DONE" "CANCELLED")))

## agenda
Ŀǰ�Ҷ�agenda������ֻ������˿�ݼ�

    (setq org-todo-keywords '((type "TODO" "STARTED" "WAITING" "|" "DONE" "CANCELLED")))

TODO�еĴ󲿷������agenda��ͼ�У�Ҳ����ִ��

## GTD����
Ŀǰ�ļ��������£�
* �ռ����񣬲������ռ��丸�ڵ�
* ��ʱΪ��������schedule�͸ı�TODO״̬
* ����schedule�������

## ��ע
����ѧϰ��ʵ��ʱ����ݣ����̱Ƚϼ򵥣�����ִ���е㷱�������ں���ѧϰ�в��ϸ��ƣ������´��ĵ���
