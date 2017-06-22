---
layout: post
title: What are Descriptors in python
categories:
- blog
---


class User:
  def __init__(self,fee=None):
    self.fee = fee
  @property
    def fee(self):
      return self.fee
  @fee.setter
    def fee(self,value):
      self.fee = value
 





