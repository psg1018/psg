package com.robot.test;

import com.dingtalk.api.DefaultDingTalkClient;
import com.dingtalk.api.DingTalkClient;
import com.dingtalk.api.request.OapiRobotSendRequest;
import com.dingtalk.api.response.OapiRobotSendResponse;
import com.taobao.api.ApiException;
import org.apache.commons.codec.binary.Base64;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Date;


public class RobotSend {
    public static void main(String[] args) throws ApiException, UnsupportedEncodingException, NoSuchAlgorithmException, InvalidKeyException {
        //获取getUrl方法得到的url
        String url = getUrl();
        DingTalkClient client = new DefaultDingTalkClient(url);
        OapiRobotSendRequest request = new OapiRobotSendRequest();
        request.setMsgtype("text");
        OapiRobotSendRequest.Text text = new OapiRobotSendRequest.Text();
        //获取当前系统时间并格式化
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String date = df.format(new Date());
        text.setContent("hello wrold！，此条消息发送时间："+date);
        request.setText(text);
        OapiRobotSendResponse response = client.execute(request);
        //输出日志 在Linux中将日志保存到log.txt中
        System.out.println("发送成功，发送时间："+date);
        ;
    }


    public static String getUrl() throws NoSuchAlgorithmException, UnsupportedEncodingException, InvalidKeyException {
        Long timestamp = System.currentTimeMillis();
        String secret = "SEC109983b9e278f6478dda10c65de9adbb7daa5452afd47ca5a593f5cf6daf434b";
        String stringToSign = timestamp + "\n" + secret;
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(secret.getBytes("UTF-8"), "HmacSHA256"));
        byte[] signData = mac.doFinal(stringToSign.getBytes("UTF-8"));
        String sign = URLEncoder.encode(new String(Base64.encodeBase64(signData)),"UTF-8");
        //定义token
        String access_token = "fff2cdb34db2e6d99409ebd568ca30208eb3e7fd4cede51cbb7e2b3a771b913d";
        //拼接字符串
        String url = "https://oapi.dingtalk.com/robot/send"+
                "?access_token=%s"+
                "&timestamp=%s"+
                "&sign=%s";
        String serverUrl= String.format(
                url,
                access_token,
                timestamp,
                sign
        );
        return serverUrl;
    }
}
