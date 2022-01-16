---
layout: post
title:  "如何备考谷歌云认证"
author: ming
categories: [ GCP, Cloud ]
tags: featured
image: assets/images/2022/GCP_cert/GCP0.png
---
谷歌云GCP的认证有十种，覆盖架构、开发运维、指定领域几个大类，可以根据自己的爱好和发展方向进行选择。与AWS认证不同， GCP只提供一门助理级认证即GCP Cloud Engineer，其他大部分为专家级Pro认证.

![]({{site.baseurl}}/assets/images/2022/GCP_cert/GCP1.png)

如果你对云或传统IT的各组成要素如计算，储存，网络，开发均有一定知识和经验，比如已经拥有其他云的架构师认证，可以直接从GCP Pro Cloud Architect入手，云服务平台的大部分功能都是相通的，只不过名字，用法，特性和命令行各不相同。如果你具备DEVOPS经验或者想了解云原生的DEVOPS过程，可以先从Cloud DevOps Engineer入手。又或者你是大数据方向，当然也可以从Data Engineer下手。GCP认证的考试多为2小时50到选择题（单选和多选），考试费用200美元，有效期仅有两年（AWS多为300美元三年）。

根据我本人的经验，我已经拥有AWS的助理架构师证书，但因为工作需要加强DEVOPS方向技能的培养，所以我先入手了GCP的DEVOPS证书，紧接着又拿下了它的架构师认证。双证的学习，上机，备考一共花费3-4个月的业余时间（和近100美刀的云使用费o(≧v≦)o， K8S的cluster还是小费$$的(･_･;)，但是谷歌认证的最大好处就是，在通过认证时即可以选一件免费礼品，均为大牌OEM的户外保暖或包包，虽然颜值一般，都是攻城狮的范儿哈哈～

![]({{site.baseurl}}/assets/images/2022/GCP_cert/GCP2.png)

首先来看谷歌[Professional Cloud DevOps Engineer](https://cloud.google.com/certification/guides/cloud-devops-engineer)官方的考察内容：

* Section 1. Applying site reliability engineering principles to a service
* Section 2. Building and implementing CI/CD pipelines for a service
* Section 3. Implementing service monitoring strategies
* Section 4. Optimizing service performance
* Section 5. Managing service incidents

第一项也是GCP区别于AWS DEVOPS最大的考察目标即是对SRE的理解，也许是Google大力倡导SRE的缘故，所以无论是官方培训课程还是考试题目都要求候选人充分理解SRE的概念和方法论，包括科技组织文化上倡导*不责怪*，鼓励*设计思考*， *原型开发快速迭代*，打破组织隔阂*建立互信包容的工程团队*氛围等等。在方法论上引入*可观测性*的概念，号召IT*监控一切*，大力推进自动化，减少无用功（防止内卷），以尽可能的实现*让系统自动保障高可靠性*的宏伟目标。SRE的实践，其实无分自建IT或云原生平台，是放之四海而皆准的运维准则。

在CI/CD方面，GCP主要考察基于Gitops和谷歌云产品如Cloud Build，Artifact Registry，GKE之间的生产线搭建，以及第三方Jenkins的整合。

云监控方面，需要了解利用GCP Cloud Monitoring(原Stackdriver)来监控计算，储存或应用等各层面的性能指标或故障报警。

在线课程最好的是谷歌官方在Coursera上提供的[Preparing for Google Cloud Certification: Cloud DevOps Engineer](https://www.coursera.org/professional-certificates/sre-devops-engineer-google-cloud)，不仅提供了非常谷歌化的技术内容讲解，还提供大量lab进行实践练习，而且每个lab都有设计完成目标，平台会自动检测你的完成度并给予评分，可以说是真正实现了学与练的全自动化。

其他在线培训平台如Udemy上面GCP的认证课程不多，内容更丰富的Acloudguru上面有全路径课程，想更全面系统学习的小伙伴可以继续进修。

接下来再来说说GCP [Professional Cloud Architect](https://cloud.google.com/certification/guides/professional-cloud-architect)，任何一家的云架构认证都是考察对自家云主力产品的**基本认知**，并没有多么深入，但覆盖度还是比较广的。对GCP来说，其计算的主力产品GCE，GAE，GKE，储存的GCS，CloudSQL数据库，都是重点考察方面，数据的分析，保管，移植和安全也是重点。按照各个产品大类进行一次系统性的梳理还是很有必要的。

* Section 1. Designing and planning a cloud solution architecture
* Section 2. Managing and provisioning a solution infrastructure
* Section 3. Designing for security and compliance
* Section 4. Analyzing and optimizing technical and business processes
* Section 5. Managing implementation
* Section 6. Ensuring solution and operations reliability

结合系统化学习和考试实际内容，我总结出重点知识点考点脑图一份，有需要的小伙伴可以在[Linkedin](https://www.linkedin.com/in/billrain/)上加我要链接～

![]({{site.baseurl}}/assets/images/2022/GCP_cert/GCP3.png)

最后祝想报名考试的同学们好运～

参考资源：

[GCPSketchnote](https://github.com/priyankavergadia/GCPSketchnote)
![where shoud you run GCP](https://github.com/priyankavergadia/GCPSketchnote/raw/main/images/ComputeOptions.jpg)
[Google Cloud Architecture Framework](https://cloud.google.com/architecture/framework)
[Best practices for enterprise organizations](https://cloud.google.com/docs/enterprise/best-practices-for-enterprise-organizations)
[The gcloud tool cheat sheet](https://cloud.google.com/sdk/docs/cheatsheet)
