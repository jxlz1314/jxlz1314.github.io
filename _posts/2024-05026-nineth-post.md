---
layout: post
title:  "记录OpenFOAM计算遇到的问题及解决方法"
categories: 教程
tags:  教程
author: JIANG Xiao
---

* content
{:toc}

# 1. 在aggParticleCloud中添加类成员报错，信息如下
#0  Foam::error::printStack(Foam::Ostream&) at ??:?
#1  Foam::sigSegv::sigHandler(int) at ??:?
#2  ? in /lib/x86_64-linux-gnu/libc.so.6
#3  Foam::Cloud<Foam::aggParticle>::fieldIOobject(Foam::word const&, Foam::IOobject::readOption) const at ??:?
#4  void Foam::particle::readFields<Foam::Cloud<Foam::aggParticle> >(Foam::Cloud<Foam::aggParticle>&) at ??:?
#5  Foam::aggParticle::readFields(Foam::Cloud<Foam::aggParticle>&) at ??:?
#6  Foam::aggParticleCloud::aggParticleCloud(Foam::fvMesh const&, Foam::word const&, bool) at ??:?
#7  ? at ??:?
#8  __libc_start_main in /lib/x86_64-linux-gnu/libc.so.6
#9  ? at ??:?

原因：在求解器主目录未更新
解决方法：在求解器目录下再wmake一次即可
