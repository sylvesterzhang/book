---
layout: post
title: JAVA通用工具类封装
tags: java
categories: backend
---

发送邮件功能、JSON解析、压缩包处理

**发送邮件功能**

1）依赖

```xml
		<dependency>
			<groupId>com.sun.mail</groupId>
			<artifactId>javax.mail</artifactId>
			<version>1.6.0</version>
		</dependency>
```

2）定义发送对象

```java
import java.util.Properties;

import javax.mail.Address;
import javax.mail.Authenticator;
import javax.mail.MessagingException;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.MimeMessage;

public class MultiMimeSender {

	private Transport transport;

	/**
	 * 构建sender对象
	 * @param smtpHost 服务地址
	 * @param smtpPort 端口
	 * @param userName 用户名
	 * @param password 密码
	 * @param isAuth 是否开启auth验证
	 */
	public MultiMimeSender(String smtpHost, int smtpPort, final String userName, final String password, boolean isAuth)
			throws MessagingException {

		Properties props = System.getProperties();

		props.put("mail.smtp.host", smtpHost);
		props.put("mail.smtp.port", smtpPort);
		props.put("mail.smtp.auth", isAuth);

		Session session = Session.getInstance(props, new Authenticator() {
			@Override
			protected PasswordAuthentication getPasswordAuthentication() {
				return new PasswordAuthentication(userName, password);
			}
		});

		this.transport = session.getTransport("smtp");

	}

	/**
	 * 发送邮件
	 * @param mimeMessage 邮件消息对象
	 * @param addresses 目标地址
	 */
	public void doSend(MimeMessage mimeMessage, Address[] addresses) throws MessagingException {
		transport.connect();
		transport.sendMessage(mimeMessage, addresses);
		transport.close();
	}
}

```

3）定义工具类

```java
package com.koal.modules.resume.util;

import java.io.File;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Date;
import java.util.Properties;

import javax.activation.DataHandler;
import javax.activation.FileDataSource;
import javax.mail.Address;
import javax.mail.BodyPart;
import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.Multipart;
import javax.mail.Session;
import javax.mail.internet.AddressException;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeBodyPart;
import javax.mail.internet.MimeMessage;
import javax.mail.internet.MimeMultipart;
import javax.mail.internet.MimeUtility;

import cn.hutool.core.util.ArrayUtil;
import org.apache.commons.lang.ArrayUtils;
import org.apache.commons.lang.StringUtils;

public class MailForwardUtil {

	public static void forwardMail(MultiMimeSender sender,String from,String[] recipients,
								   String emlFilePath) throws Exception {

		recipients = ArrayUtil.removeBlank(recipients);

		//从文件中读取出MimeMessage对象
		MimeMessage fileMessage = getMimeMessageFromFile(emlFilePath);
		Multipart msgMultpart = (Multipart) fileMessage.getContent();


		MimeMessage mimeMessage = new MimeMessage(Session.getInstance(new Properties()));

		//基本信息
		mimeMessage.setHeader("X-Mailer", "LOTONtechEmail");
		mimeMessage.setSentDate(new Date());
		mimeMessage.setFrom(new InternetAddress(from));
		mimeMessage.setRecipients(Message.RecipientType.TO, getAdresses(recipients));

		//主题
		String oldSubject = getSubjectFromMimeMessage(fileMessage);
		String newSubject = "转发: " + oldSubject;
		mimeMessage.setSubject(newSubject);

		//正文文本
		MimeBodyPart mbp1 = new MimeBodyPart();
		mbp1.setContent(msgMultpart);
		Multipart mp = new MimeMultipart();
		mp.addBodyPart(mbp1);

		mimeMessage.setContent(mp,msgMultpart.getBodyPart(0).getContentType());

		sender.doSend(mimeMessage,getAdresses(recipients));

	}

	/**
	 * 从eml文件中读取出MimeMessage对象
	 * @param emlFilePath 文件地址
	 */
	public static MimeMessage getMimeMessageFromFile(String emlFilePath) throws IOException, MessagingException {
		return MailUtils.decodeMimeMessage(new File(emlFilePath));
	}

	/**
	 * 从MimeMessage对象中读取出主题
	 * @param mimeMessage MimeMessage对象
	 */
	public static String getSubjectFromMimeMessage(MimeMessage mimeMessage) throws Exception {
		return MailUtils.getSubject(mimeMessage);
	}

	/**
	 * 发送邮件
	 * @param sender 邮件消息对象
	 * @param from 发送者
	 * @param recipients 接收者
	 * @param cc 抄送者
	 * @param subject 主题
	 * @param text 正文
	 * @param attachFilePaths 目标地址
	 */
	public static void sendMail(MultiMimeSender sender, String from, String[] recipients,String[] cc ,
								String subject, String text, String[] attachFilePaths)
			throws MessagingException, UnsupportedEncodingException {

		MimeMessage mimeMessage = new MimeMessage(Session.getInstance(new Properties()));

		recipients = ArrayUtil.removeBlank(recipients);
		cc = ArrayUtil.removeBlank(cc);

		//所有接收者
		String[] allRecipients = (String[]) ArrayUtils.addAll(recipients,cc);

		//邮件基本信息
		mimeMessage.setHeader("X-Mailer", "LOTONtechEmail");
		mimeMessage.setSentDate(new Date());
		mimeMessage.setFrom(new InternetAddress(from));
		mimeMessage.setRecipients(Message.RecipientType.TO, getAdresses(recipients));
		if(cc.length>=1){
			mimeMessage.setRecipients(Message.RecipientType.CC, getAdresses(cc));
		}

		//邮件主题
		mimeMessage.setSubject(subject);

		//正文文本
		MimeBodyPart mbp1 = new MimeBodyPart();
		mbp1.setText(text);
		Multipart mp = new MimeMultipart();
		mp.addBodyPart(mbp1);

		//附件
		if(attachFilePaths!=null){
			for (String filePath : attachFilePaths) {
				BodyPart filebp = new MimeBodyPart();
				FileDataSource fileds = new FileDataSource(filePath);
				filebp.setDataHandler(new DataHandler(fileds));
				filebp.setFileName(MimeUtility.encodeText(fileds.getName()));
				mp.addBodyPart(filebp);
			}
		}

		mimeMessage.setContent(mp);

		sender.doSend(mimeMessage, getAdresses(allRecipients));

	}

	/**
	 * 将字符串数组转换成地址数组
	 * @param strArray 字符串数组
	 */
	private static Address[] getAdresses(String[] strArray) throws AddressException {
		ArrayList<Address> list = new ArrayList<>();
		for (int i = 0; i < strArray.length; i++) {
			list.add(new InternetAddress(strArray[i]));
		}
		return (Address[]) list.toArray(new Address[strArray.length]);
	}


}

```

---

**JSON解析**

1）依赖

```xml
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>fastjson</artifactId>
			<version>1.2.72</version>
		</dependency>
```

2）工具类

```java
package com.koal.modules.resume.util;

import com.alibaba.fastjson.JSON;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.Stack;

public class JsonUtils {
	/**
	 * 从json字符串中读取出属性值
	 * 
	 * @param jsonstr json字符串
	 * @param key     属性，如 "result.college"
	 */
	public static String getValueFromJsonString(String jsonstr, String key) {
		
		String[] stringArray = key.split("\\.");
		String str = jsonstr;
		
		try {
			for (int i = 0; i < stringArray.length; i++) {
				Map<?, ?> map = getMapFromJsonString(str);
				str = map.get(stringArray[i]).toString();
			}
		} catch (Exception e) {
			str = "";
		}
		
		return str;
	}

	/**
	 * 从json文件中读取出json字符串
	 * 
	 * @param jsonFileDir json文件路径
	 */
	public static String getJsonStringFromJsonFile(String jsonFileDir) throws Exception {
		String jo = null;
		StringBuilder result = new StringBuilder();
		// 构造一个BufferedReader类来读取文件
		BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream(jsonFileDir), "UTF-8"));
		String s = null;
		while ((s = br.readLine()) != null) {
			// 使用readLine方法，一次读一行
			result.append(System.lineSeparator() + s);
		}
		br.close();
		jo = result.toString();
		return jo;
	}

	/**
	 * 将json字符串转换成map对象
	 * 
	 * @param jsonstr json字符串
	 */
	private static Map<?, ?> getMapFromJsonString(String jsonstr) {
		return (Map<?, ?>) JSON.parse(jsonstr);
	}

	/**
	 * 把json对象数据存储在map以键值对的形式存储，只存储叶节点
	 * @param stObj
	 * @param resultMap
	 */
	private static void JsonToMap(Stack<JSONObject> stObj, Map<String, Object> resultMap) throws Exception {
		if(stObj == null && stObj.pop() == null){
			return ;
		}
		JSONObject json = stObj.pop();
		Iterator it = json.keys();
		while(it.hasNext()){
			String key = (String) it.next();
			//得到value的值
			Object value = json.get(key);
			//System.out.println(value);
			if(value instanceof JSONObject)
			{
				stObj.push((JSONObject)value);
				//递归遍历
				JsonToMap(stObj,resultMap);
			}
			else {
				resultMap.put(key, value);
			}
		}
	}

	/**
	 * 把json字符串完全转换成map
	 * @param jsonStr json字符串
	 */
	public static Map<String,Object> JsonToMap(String jsonStr) throws Exception {
		//转换成map对象
		JSONObject obj = new JSONObject(jsonStr);
		Stack<JSONObject> stObj = new Stack<JSONObject>();
		stObj.push(obj);
		Map<String ,Object> resultMap = new HashMap<>();
		JsonToMap(stObj,resultMap);
		return resultMap;
	}
}

```

---

**压缩包处理**

1）依赖

```xml
<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-compress -->
		<dependency>
			<groupId>org.apache.commons</groupId>
			<artifactId>commons-compress</artifactId>
			<version>1.20</version>
		</dependency>
```

2）工具类

```java
package com.koal.modules.resume.util;

import lombok.extern.slf4j.Slf4j;
import org.apache.commons.compress.archivers.zip.*;
import org.apache.commons.compress.utils.IOUtils;
import org.apache.commons.lang.StringUtils;

import java.io.*;
import java.util.ArrayList;
import java.util.List;

/**
 * 解压Zip文件工具类
 *
 */
@Slf4j
public class ZipUtils
{
    public static int BUFFER_SIZE = 2048;
    public static List<String> unZip(File zipfile, String destDir)
            throws Exception {
        if (StringUtils.isBlank(destDir)) {
            destDir = zipfile.getParent();
        }
        destDir = destDir.endsWith(File.separator) ? destDir : destDir
                + File.separator;
        ZipArchiveInputStream is = null;
        List<String> fileNames = new ArrayList<String>();

        try {
            is = new ZipArchiveInputStream(new BufferedInputStream(
                    new FileInputStream(zipfile), BUFFER_SIZE),"utf8");
            unzipProccess(is,fileNames,destDir);
        } catch (Exception e) {
            try {
                log.info("解压过程：utf8报错，使用gbk");
                is = new ZipArchiveInputStream(new BufferedInputStream(
                        new FileInputStream(zipfile), BUFFER_SIZE),"gbk");
                unzipProccess(is,fileNames,destDir);
            } catch (IOException exception) {
                log.error("解压过程报错：" + exception);
                throw exception;
            }
        } finally {
            IOUtils.closeQuietly(is);
        }
        return fileNames;
    }

    private static void unzipProccess(ZipArchiveInputStream is, List<String> fileNames, String destDir) throws IOException {
        ZipArchiveEntry entry = null;
        while ((entry = is.getNextZipEntry()) != null) {
            fileNames.add(entry.getName());
            if (entry.isDirectory()) {
                File directory = new File(destDir, entry.getName());
                directory.mkdirs();
            } else {
                OutputStream os = null;
                File entryFile = new File(destDir, entry.getName());
                File path = new File(entryFile.getParent());
                path.mkdirs();
                try {
                    os = new BufferedOutputStream(new FileOutputStream(
                            new File(destDir, entry.getName())),
                            BUFFER_SIZE);
                    IOUtils.copy(is, os);
                } finally {
                    IOUtils.closeQuietly(os);
                }
            }
        }
    }

    /**
     * 解压Zip文件
     * @param  zipfile 文件目录
     * @param destDir 文件解压的目标目录
     */
    public static List<String> unZip(String zipfile, String destDir) throws Exception {
        File zipFile = new File(zipfile);
        return unZip(zipFile, destDir);
    }

    public static void compressFiles2Zip(File[] files, String zipFilePath) {
        if (files != null && files.length > 0) {
            ZipArchiveOutputStream zaos = null;
            File f = new File(zipFilePath);
            if(f.isDirectory())
            {
                System.out.println("isDirectory");
                return;
            }
            if(f.exists())
            {
                f.delete();
            }
            try {
                File zipFile = new File(zipFilePath);
                zaos = new ZipArchiveOutputStream(zipFile);
                zaos.setUseZip64(Zip64Mode.AsNeeded);
                int index = 0;
                for (File file : files) {
                    if (file != null) {
                        ZipArchiveEntry zipArchiveEntry = new ZipArchiveEntry(
                                file, new File(file.getParent()).getName()+File.separator+file.getName());
                        zaos.putArchiveEntry(zipArchiveEntry);
                        InputStream is = null;
                        try {
                            is = new BufferedInputStream(new FileInputStream(
                                    file));
                            byte[] buffer = new byte[1024 * 5];
                            int len = -1;
                            while ((len = is.read(buffer)) != -1) {
                                // 把缓冲区的字节写入到ZipArchiveEntry
                                zaos.write(buffer, 0, len);
                            }
                            // Writes all necessary data for this entry.
                            zaos.closeArchiveEntry();
                        } catch (Exception e) {
                            throw new RuntimeException(e);
                        } finally {
                            if (is != null)
                                is.close();
                        }
                    }
                }
                zaos.finish();
            } catch (Exception e) {
                throw new RuntimeException(e);
            } finally {
                try {
                    if (zaos != null) {
                        zaos.close();
                    }
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
        }

    }
}

```

