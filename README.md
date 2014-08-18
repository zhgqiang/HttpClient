-HttpClient
===========

通过HttpClient将网页源代码下载到本地 进行数据分析


import java.io.BufferedOutputStream;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.util.ArrayList;
import java.util.List;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import org.apache.commons.httpclient.HttpClient;
import org.apache.commons.httpclient.HttpException;
import org.apache.commons.httpclient.methods.GetMethod;

public class HttpDemo2 {
	public static final int LOGON_PORT = 80;
	public String url = "http://www.lagou.com/jobs/list_Java?kd=Java&spc=1&pl=&gj=&xl=&yx=&gx=&st=&labelWords=hot&lc=&workAddress=&city=%E5%85%A8%E5%9B%BD&requestId=&pn=";
	int page = 1;
	HttpClient client = new HttpClient();

	public List<String> findUrl() throws IOException {
		List<String> list = new ArrayList<String>();
		for (int j = 1; j <= page; j++) {
			client.getHostConfiguration().setHost(url + j, LOGON_PORT);
			GetMethod get = new GetMethod(url);
			get.getParams().setContentCharset("utf-8");
			String resposeString = "";
			// String regex =
			// "(http://www\\.lagou\\.com/jobs/)\\w+((\\.html\\?source=search)|(\\?[\\w=&%]+#))";
			String regex = "(http:)//([a-z0-9A-Z]+\\.)+\\w{2,3}/.+/\\d+\\.\\w+";
			client.executeMethod(get);
			resposeString = get.getResponseBodyAsString();
			get.releaseConnection();
			byte[] bytes = resposeString.getBytes();
			// File file = new File("D:" + File.separator + "java.txt");
			// PrintWriter writer = new PrintWriter(file);
			// writer.write(resposeString);
			// System.out.println("OK");
			// writer.close();
			Pattern pattern = Pattern.compile(regex);
			Matcher matcher = pattern.matcher(resposeString);
			while (matcher.find()) {
				list.add(matcher.group());
			}
		}
		return list;
	}

	public String getFile() throws IOException {
		List<String> list = this.findUrl();
		for (int i = 0; i < list.size(); i++) {
			System.out.println(list.get(i));
			client.getHostConfiguration().setHost(list.get(i), LOGON_PORT);
			GetMethod get = new GetMethod(list.get(i));
			get.getParams().setContentCharset("utf-8");
			client.executeMethod(get);
			String resposeString = get.getResponseBodyAsString();
			PrintWriter writer = new PrintWriter(new OutputStreamWriter(
					new BufferedOutputStream(new FileOutputStream("D:/worm/"
							+ System.currentTimeMillis() + ".txt", true)),
					"utf-8"));
			writer.println(resposeString);
			writer.close();
		}
		return "ok";
	}
}
