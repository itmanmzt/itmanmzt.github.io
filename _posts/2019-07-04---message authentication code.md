---
layout: post
#标题配置
title: 短信验证码
#时间配置
date:   2019-07-04 17:14:00 +0800
#小类配置
tag: 小功能


---

* content
{:toc}
---
---

# message authentication code
首先要选择一个短信服务平台，本文介绍的是阿里云，具体操作步骤如下：<br>
1、注册阿里云账号并登陆；<br>
2、开通短信服务；<br>
3、配置短信签名；<br>
4、配置短信模板；<br>
5、开通RAM服务；<br>
6、配置RAM服务；<br>
7、观看开发文档，下载对应的jar包和观看对应的demo；<br>
<b>登陆阿里云后，按下面图示步骤进行操作：</b>
## 开通短信服务
![](https://itmanmzt.github.io/styles/images/duanxin/001.jpg){:align="center"}
然后按要求开通服务......
## 配置短信签名和短信模板
![](https://itmanmzt.github.io/styles/images/duanxin/002.jpg){:align="center"}
根据自己的业务范围选择国内或国际业务进行配置
![](https://itmanmzt.github.io/styles/images/duanxin/003.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/duanxin/004.jpg){:align="center"}
配置并提交申请之后就等待审核通过.....
## 开通和配置PAM服务
[点击这里开通](https://buy.aliyun.com/ram){:target="_blank"}<br>
开通完成后按下面图示进行配置：<br>
首先按要求创建用户：
![](https://itmanmzt.github.io/styles/images/duanxin/005.jpg){:align="center"}
创建用户后系统会给你一个AccessKey ID 和一个AccessKeySecret，一定要记下来否则页面关闭就看不了了，切记！<br>
接下来进行权限设置：
![](https://itmanmzt.github.io/styles/images/duanxin/006.jpg){:align="center"}
从左边找出下面的四个权限添加到右边：
![](https://itmanmzt.github.io/styles/images/duanxin/007.jpg){:align="center"}
到了这里配置工作就已经完成，下面就需要进行开发工作
## 开发的前期准备
[点击这里观看开发文档并下载SDK](https://help.aliyun.com/document_detail/55359.html?spm=a2c4g.11186623.2.18.65be3144ePmp16){:target="_blank"}<br>
本文使用Java语言进行开发，所以需要在项目中加入下面两个jar包：
![](https://itmanmzt.github.io/styles/images/duanxin/008.jpg){:align="center"}
## 后台开发代码
```
import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.IAcsClient;
import com.aliyuncs.dysmsapi.model.v20170525.QuerySendDetailsRequest;
import com.aliyuncs.dysmsapi.model.v20170525.QuerySendDetailsResponse;
import com.aliyuncs.dysmsapi.model.v20170525.SendSmsRequest;
import com.aliyuncs.dysmsapi.model.v20170525.SendSmsResponse;
import com.aliyuncs.dysmsapi.transform.v20170525.SendSmsResponseUnmarshaller;
import com.aliyuncs.exceptions.ClientException;
import com.aliyuncs.http.FormatType;
import com.aliyuncs.http.HttpResponse;
import com.aliyuncs.profile.DefaultProfile;
import com.aliyuncs.profile.IClientProfile;

import java.nio.charset.Charset;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.UUID;

/**
 * Created on 17/6/7.
 * 短信API产品的DEMO程序,工程中包含了一个SmsDemo类，直接通过
 * 执行main函数即可体验短信产品API功能(只需要将AK替换成开通了云通信-短信产品功能的AK即可)
 * 工程依赖了2个jar包(存放在工程的libs目录下)
 * 1:aliyun-java-sdk-core.jar
 * 2:aliyun-java-sdk-dysmsapi.jar
 *
 * 备注:Demo工程编码采用UTF-8
 * 国际短信发送请勿参照此DEMO
 */
public class SmsDemo {

    //产品名称:云通信短信API产品,开发者无需替换
    static final String product = "Dysmsapi";
    //产品域名,开发者无需替换
    static final String domain = "dysmsapi.aliyuncs.com";

    // TODO 此处需要替换成开发者自己的AK(在阿里云访问控制台寻找)
    static final String accessKeyId = "";
    static final String accessKeySecret = "";

    public static SendSmsResponse sendSms() throws ClientException {

        //可自助调整超时时间
        System.setProperty("sun.net.client.defaultConnectTimeout", "10000");
        System.setProperty("sun.net.client.defaultReadTimeout", "10000");

        //初始化acsClient,暂不支持region化
        IClientProfile profile = DefaultProfile.getProfile("cn-hangzhou", accessKeyId, accessKeySecret);
        DefaultProfile.addEndpoint("cn-hangzhou", "cn-hangzhou", product, domain);
        IAcsClient acsClient = new DefaultAcsClient(profile);

        //组装请求对象-具体描述见控制台-文档部分内容
        SendSmsRequest request = new SendSmsRequest();
        //必填:待发送手机号
        request.setPhoneNumbers("");
        //必填:短信签名-可在短信控制台中找到
        request.setSignName("");
        //必填:短信模板-可在短信控制台中找到
        request.setTemplateCode("");
        //可选:模板中的变量替换JSON串,如模板内容为"亲爱的${name},您的验证码为${code}"时,此处的值为
        request.setTemplateParam("{\"name\":\"Tom\", \"code\":\"1314\"}");

        //选填-上行短信扩展码(无特殊需求用户请忽略此字段)
        //request.setSmsUpExtendCode("90997");

        //可选:outId为提供给业务方扩展字段,最终在短信回执消息中将此值带回给调用者
        request.setOutId("yourOutId");

        //hint 此处可能会抛出异常，注意catch
        SendSmsResponse sendSmsResponse = acsClient.getAcsResponse(request);

        return sendSmsResponse;
    }


    public static QuerySendDetailsResponse querySendDetails(String bizId) throws ClientException {

        //可自助调整超时时间
        System.setProperty("sun.net.client.defaultConnectTimeout", "10000");
        System.setProperty("sun.net.client.defaultReadTimeout", "10000");

        //初始化acsClient,暂不支持region化
        IClientProfile profile = DefaultProfile.getProfile("cn-hangzhou", accessKeyId, accessKeySecret);
        DefaultProfile.addEndpoint("cn-hangzhou", "cn-hangzhou", product, domain);
        IAcsClient acsClient = new DefaultAcsClient(profile);

        //组装请求对象
        QuerySendDetailsRequest request = new QuerySendDetailsRequest();
        //必填-号码
        request.setPhoneNumber("");
        //可选-流水号
        request.setBizId(bizId);
        //必填-发送日期 支持30天内记录查询，格式yyyyMMdd
        SimpleDateFormat ft = new SimpleDateFormat("yyyyMMdd");
        request.setSendDate(ft.format(new Date()));
        //必填-页大小
        request.setPageSize(10L);
        //必填-当前页码从1开始计数
        request.setCurrentPage(1L);

        //hint 此处可能会抛出异常，注意catch
        QuerySendDetailsResponse querySendDetailsResponse = acsClient.getAcsResponse(request);

        return querySendDetailsResponse;
    }

    public static void main(String[] args) throws ClientException, InterruptedException {

        //发短信
        SendSmsResponse response = sendSms();
        System.out.println("短信接口返回的数据----------------");
        System.out.println("Code=" + response.getCode());
        System.out.println("Message=" + response.getMessage());
        System.out.println("RequestId=" + response.getRequestId());
        System.out.println("BizId=" + response.getBizId());

        Thread.sleep(3000L);

        //查明细
        if(response.getCode() != null && response.getCode().equals("OK")) {
            QuerySendDetailsResponse querySendDetailsResponse = querySendDetails(response.getBizId());
            System.out.println("短信明细查询接口返回数据----------------");
            System.out.println("Code=" + querySendDetailsResponse.getCode());
            System.out.println("Message=" + querySendDetailsResponse.getMessage());
            int i = 0;
            for(QuerySendDetailsResponse.SmsSendDetailDTO smsSendDetailDTO : querySendDetailsResponse.getSmsSendDetailDTOs())
            {
                System.out.println("SmsSendDetailDTO["+i+"]:");
                System.out.println("Content=" + smsSendDetailDTO.getContent());
                System.out.println("ErrCode=" + smsSendDetailDTO.getErrCode());
                System.out.println("OutId=" + smsSendDetailDTO.getOutId());
                System.out.println("PhoneNum=" + smsSendDetailDTO.getPhoneNum());
                System.out.println("ReceiveDate=" + smsSendDetailDTO.getReceiveDate());
                System.out.println("SendDate=" + smsSendDetailDTO.getSendDate());
                System.out.println("SendStatus=" + smsSendDetailDTO.getSendStatus());
                System.out.println("Template=" + smsSendDetailDTO.getTemplateCode());
            }
            System.out.println("TotalCount=" + querySendDetailsResponse.getTotalCount());
            System.out.println("RequestId=" + querySendDetailsResponse.getRequestId());
        }

    }
}
```
运行效果展示：
![](https://itmanmzt.github.io/styles/images/duanxin/009.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/duanxin/010.jpg){:align="center"}<br><br>



  <br>

<br>

<center>有Marin的地方就有你的收获</center>