---
layout: post
#标题配置
title: 图中点击验证码
#时间配置
date:   2019-07-06 13:44:00 +0800
#小类配置
tag: 小功能



---

* content
{:toc}
---
---

# picture code

项目核心思路：<br>
1、实现过程：用户请求登陆——服务器生成图片验证码并转发到用户浏览器——用户完成验证过程并提交——服务器进行检验；<br>
2、生成验证码的过程：定义图片组，选择要验证的图片组并保存其信息，并根据定义的要发给用户的图片数和要验证的图片组的图片组随机选择其他的图片组一起拼凑出一张总的验证图发给用户；<br>
3、验证的实现方式：保存目标图片在大图片的位置，通过前端获取用户点击位置与服务器保存的位置进行比对来实现验证；<br>
4、验证过程的核心：服务器要针对每个用户保存其正确验证信息，方便后续与用户提交的验证信息进行检验，我们这里选择使用缓存（对于缓存，要考虑大规模访问对内存的消耗，应该设置过期时间并且在完成验证后主动清除缓存）；<br>
5、保证验证的安全可靠：在生产环境中，应该存在多个用户和请求验证，对于不同的用户生产的图片验证码可能是相同的，所以要区分不同用户的不同请求，在这里我们用到了cookie；<br>

前端页面：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <meta charset="UTF-8">
    <title>图中点击验证码</title>
    <style type="text/css">
        .loginBox {
            background: white;
            width: 100%;
            height: 700px;
            margin: auto 0;
        }

        .loginTop {
            width: 100%;
            height: 100px;
            background: #EBEFF0;
            text-align: center;
        }

        .login {
            list-style-type: none;
        }

        .login_cont {
            margin-top: 80px;
            width: 450px;
            height: 500px;
            border: 1px solid black;
            font-size: 20px;
            margin-left: 38%;
            margin-bottom: 100px;
        }

        .login_btn {
            width: 300px;
            height: 40px;
            font-size: 20px;
            border-radius: 4px;
        }

        .loginImage {
            margin: 20px;
        }

        input {
            width: 300px;
            height: 30px;
        }

        .login {
            margin-left: 50px;
        }

        a {
            font-size: 15px;
        }

        .loginBottom {
            background: #EBEFF0;
            width: 100%;
            height: 150px;
            text-align: center;
        }
    </style>
    <script type="text/javascript">
        var index = 1;

        function addImg(e) {
            var parentDiv = document.getElementById("insert");

            var topValue = 0, leftValue = 0;
            var obj = parentDiv;
            while (obj) {
                leftValue += obj.offsetLeft;
                topValue += obj.offsetTop;
                obj = obj.offsetParent;
            }
               //e = e || window.event;这里感谢RayZan-J的提供，使火狐浏览器可以点击
			e=arguments.callee.caller.arguments[0] || window.event;
            var left = e.clientX + document.body.scrollLeft - document.body.clientLeft - 10;//"-110px";
            var top = e.clientY + document.body.scrollTop - document.body.clientTop - 10;//"-110px";
            var imgDivId = "img_" + index++;

            var newDiv = document.createElement("div");
            parentDiv.appendChild(newDiv);

            newDiv.id = imgDivId;
            newDiv.style.position = "relative";
            newDiv.style.zIndex = index;
            newDiv.style.width = "20px";
            newDiv.style.height = "20px";
            newDiv.style.top = top - topValue - 150 + 10 + "px";
            newDiv.style.left = left - leftValue - 300 + "px";
            newDiv.style.display = "inline";
            newDiv.setAttribute("onclick", "removeSelf('" + imgDivId + "')");

            var img = document.createElement("img");
            newDiv.appendChild(img);

            img.src = "../../img/logo.jpg";
            img.style.width = "20px";
            img.style.height = "20px";
            img.style.top = "0px";
            img.style.left = "0px";
            img.style.position = "absolute";
            img.style.zIndex = index;

        }

        function removeSelf(id) {
            document.getElementById("insert").removeChild(document.getElementById(id));
        }

        function login() {
            var parentDiv = document.getElementById("insert");
            var nodes = parentDiv.childNodes;
            var result = '';
            for (var i = 0; i < nodes.length; i++) {
                var id = nodes[i].id;
                if (id && id.startsWith('img_')) {
                    var top = document.getElementById(id).style.top;
                    var left = document.getElementById(id).style.left;
                    result = result + top.replace('px', '') + ',' + left.replace('px', '') + ';';
                }
            }
            document.getElementById('location').value = result.substr(0, result.length - 1);
            document.getElementById('loginForm').submit();
        }
    </script>


</head>
<body>
<div class="loginBox">
    <div class="login_cont">
        <ul class="login">
            <form id="loginForm" action="${pageContext.request.contextPath}/login" method="post">
                <input type="hidden" id="location" name="location">
                <li class="l_tit">邮箱/用户名/手机号</li>
                <li class="mb_10"><input type="text" name="userName" class="login_input user_icon"></li>
                <li class="l_tit">密码</li>
                <li class="mb_10"><input type="password" name="password" class="login_input user_icon"></li>
                <li>选出图片中的"${tip}"</li>
                <div id="insert">
                    <img src="${pageContext.request.contextPath}/assets/daan/${file}" height="150" width="300" onclick="addImg()" >
                </div>
                </li>
                <li><input type="button" value="登录" class="login_btn" onclick="login();"></li>
                <br/>
                <a>使用官方合作账号登录<br/>
                    QQ|网易|新浪微博|腾讯微博</a>
            </form>
        </ul>
    </div>
</div>
</body>
</html>
```

实体类：<br><br>
缓存类：
```
public class Cache {
    private static Map<String, ImageResult> cache=new HashMap<String, ImageResult>();
    public static void put(String note,ImageResult ir){
        cache.put(note,ir);
    }
    public static ImageResult get(String note){
        return cache.get(note);

    }
    public static void remove(String note){
        cache.remove(note);
    }
}
```
图片处理相关类：
```
public class ImageGroup {

    private String name;

    private int count;

    private Set<String> images;

    public ImageGroup(String name,int count,String...imageNames){
        this.name=name;
        this.count=count;
        this.images=new HashSet<String>();
        this.images.addAll(Arrays.asList(imageNames));
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getCount() {
        return count;
    }

    public void setCount(int count) {
        this.count = count;
    }

    public Set<String> getImages() {
        return images;
    }

    public void setImages(Set<String> images) {
        this.images = images;
    }
}
```
```
public class GenerateImageGroup {

    private ImageGroup keyGroup;
    private List<ImageGroup> groups;

    public GenerateImageGroup(ImageGroup keyGroup, List<ImageGroup> groups) {
        this.keyGroup = keyGroup;
        this.groups = groups;
    }

    public ImageGroup getKeyGroup() {
        return keyGroup;
    }

    public void setKeyGroup(ImageGroup keyGroup) {
        this.keyGroup = keyGroup;
    }

    public List<ImageGroup> getGroups() {
        return groups;
    }

    public void setGroups(List<ImageGroup> groups) {
        this.groups = groups;
    }
}

```
```
public class BufferedImageWarp {

    private boolean key;

    private BufferedImage bufferedImage;

    public BufferedImageWarp(boolean key, BufferedImage bufferedImage) {
        this.key = key;
        this.bufferedImage = bufferedImage;
    }

    public boolean isKey() {
        return key;
    }

    public void setKey(boolean key) {
        this.key = key;
    }

    public BufferedImage getBufferedImage() {
        return bufferedImage;
    }

    public void setBufferedImage(BufferedImage bufferedImage) {
        this.bufferedImage = bufferedImage;
    }
}

```
```
public class ImageResult {

    private String name;
    private Set<Integer> keySet;
    private String uniqueKey;
    private String tip;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Set<Integer> getKeySet() {
        return keySet;
    }

    public void setKeySet(Set<Integer> keySet) {
        this.keySet = keySet;
    }

    public String getUniqueKey() {
        return uniqueKey;
    }

    public void setUniqueKey(String uniqueKey) {
        this.uniqueKey = uniqueKey;
    }

    public String getTip() {
        return tip;
    }

    public void setTip(String tip) {
        this.tip = tip;
    }
}

```
图片处理类：
```
public class Image {

    private static Map<String, ImageGroup> imageGroupMap = new HashMap<String, ImageGroup>();
    private static Map<Integer, Map<String, ImageGroup>> countGroupMap = new HashMap<Integer, Map<String, ImageGroup>>();

    /**
     * 生成图片     * @throws IOException
     */
    public static ImageResult generateImage(ServletRequest request) throws IOException {
        initImage();
        GenerateImageGroup generateImageGroup = randImageGroups();
        List<BufferedImageWarp> imageWarps = new ArrayList<BufferedImageWarp>();
        String realPath = request.getServletContext().getRealPath("/assets/");

        for (ImageGroup group : generateImageGroup.getGroups()) {
            for (String imgName : group.getImages()) {
                imageWarps.add(new BufferedImageWarp(false, getBufferImage(realPath+File.separator+imgName)));
            }
        }
        for (String imgName : generateImageGroup.getKeyGroup().getImages()) {
            imageWarps.add(new BufferedImageWarp(true,getBufferImage(realPath+File.separator+imgName)));
        }

        return meregeImage(request,imageWarps, generateImageGroup.getKeyGroup().getName());

    }

    /**
     * 随机生成图片
     *
     * @return
     */
    public static GenerateImageGroup randImageGroups() {
        List<ImageGroup> result = new ArrayList<ImageGroup>();
        int num = random(0, imageGroupMap.size() - 1);
        //获取相关的需要选中的key
        String name = new ArrayList<String>(imageGroupMap.keySet()).get(num);
        ImageGroup keyGroup = imageGroupMap.get(name);

        Map<Integer, Map<String, ImageGroup>> thisCountGroup = new HashMap<Integer, Map<String, ImageGroup>>(countGroupMap);

        thisCountGroup.get(keyGroup.getCount()).remove(name);
        // 假设总量8个，每种名称图片只有2个或者4个，为了逻辑简单些
        int leftCount = 8 - keyGroup.getCount();
        if (leftCount == 4) {
            // 继续产生随机数
            if (new Random().nextInt() % 2 == 0) {
                //判断产生的随机数是否被二整除是则产生4个图片的组合
                List<ImageGroup> groups = new ArrayList<ImageGroup>(thisCountGroup.get(4).values());

                if (groups.size() > 1) {
                    num = random(0, groups.size() - 1);
                } else {
                    num = 0;
                }
                result.add(groups.get(num));
            } else {
                //为奇数的时候则是2个2个的组合
                List<ImageGroup> groups = new ArrayList<ImageGroup>(thisCountGroup.get(2).values());
                int num1 = random(0, groups.size() - 1);
                result.add(groups.get(num1));

                int num2 = random(0, groups.size() - 1, num1);
                result.add(groups.get(num2));
            }
        } else if (leftCount == 6) {
            if (new Random().nextInt() % 2 == 0) {
                //偶数2+4+2
                List<ImageGroup> groups1 = new ArrayList<ImageGroup>(thisCountGroup.get(4).values());
                int num1 = random(0, groups1.size() - 1);
                result.add(groups1.get(num1));

                List<ImageGroup> groups2 = new ArrayList<ImageGroup>(thisCountGroup.get(2).values());
                int num2 = random(0, groups2.size() - 1);
                result.add(groups2.get(num2));
            } else {
                List<ImageGroup> groups = new ArrayList<ImageGroup>(thisCountGroup.get(2).values());
                int num1 = random(0, groups.size() - 1);
                result.add(groups.get(num1));

                int num2 = random(0, groups.size() - 1, num1);
                result.add(groups.get(num2));

                int num3 = random(0, groups.size() - 1, num1, num2);
                result.add(groups.get(num3));
            }
        } else if (leftCount == 2) {
            List<ImageGroup> groups = new ArrayList<ImageGroup>(thisCountGroup.get(2).values());
            result.add(groups.get(random(0, groups.size() - 1)));
        }
        return new GenerateImageGroup(keyGroup, result);
    }


    private static BufferedImage getBufferImage(String fileUrl) throws IOException {
        //这个目录是你自己存放照片的目录，这里我存放在G盘下
        File f = new File(fileUrl);
        return ImageIO.read(f);
    }

    /**
     * 初始化图片
     */
    public static void initImage() {
        ImageGroup group1 = new ImageGroup("包包", 4, "baobao/1.jpg", "baobao/2.jpg", "baobao/3.jpg", "baobao/4.jpg");
        ImageGroup group2 = new ImageGroup("老虎", 4, "laohu/1.jpg", "laohu/2.jpg", "laohu/3.jpg", "laohu/4.jpg");
        ImageGroup group3 = new ImageGroup("糖葫芦", 4, "tanghulu/1.jpg", "tanghulu/2.jpg", "tanghulu/3.jpg", "tanghulu/4.jpg");
        ImageGroup group4 = new ImageGroup("小慕", 4, "xiaomu/1.jpg", "xiaomu/2.jpg", "xiaomu/3.jpg", "xiaomu/4.jpg");
        ImageGroup group5 = new ImageGroup("柚子", 4, "youzi/1.jpg", "youzi/2.jpg", "youzi/3.jpg", "youzi/4.jpg");
        ImageGroup group6 = new ImageGroup("订书机", 2, "dingshuji/1.jpg", "dingshuji/2.jpg");
        ImageGroup group7 = new ImageGroup("蘑菇", 2, "mogu/1.jpg", "mogu/2.jpg");
        ImageGroup group8 = new ImageGroup("磁铁", 2, "xitieshi/1.jpg", "xitieshi/2.jpg");
        ImageGroup group9 = new ImageGroup("土豆", 2, "tudou/1.jpg", "tudou/2.jpg");
        ImageGroup group10 = new ImageGroup("兔子", 2, "tuzi/1.jpg", "tuzi/2.jpg");
        ImageGroup group11 = new ImageGroup("仙人球", 2, "xianrenqiu/1.jpg", "xianrenqiu/2.jpg");

        initMap(group1, group2, group3, group4, group5, group6, group7, group8, group9, group10, group11);
    }

    /**
     * 初始化图     * @param groups
     */
    public static void initMap(ImageGroup... groups) {
        for (ImageGroup group : groups) {
            imageGroupMap.put(group.getName(), group);
            if (!countGroupMap.containsKey(group.getCount())) {
                countGroupMap.put(group.getCount(), new HashMap<String, ImageGroup>());
            }
            countGroupMap.get(group.getCount()).put(group.getName(), group);
        }
    }

    /**
     * 获取随机数
     */
    private static int random(int min, int max) {
        Random random = new Random();
        return random.nextInt(max - min + 1) + min;
    }

    private static int random(int min, int max, Integer... not) {
        int num = random(min, max);
        List<Integer> notList = Arrays.asList(not);
        while (notList.contains(num)) {
            num = random(min, max);
        }
        return num;
    }

    //拼接图片
    private static ImageResult meregeImage(ServletRequest request, List<BufferedImageWarp> imageWarps, String tip) throws IOException {
        Collections.shuffle(imageWarps);
        int width = 100;
        int height = 100;
        int totalWidth = width*4;

        BufferedImage destImage = new BufferedImage(totalWidth, 200, BufferedImage.TYPE_INT_BGR);
        int x1 = 0;
        int x2 = 0;
        int order = 0;
        List<Integer> keyOrderList = new ArrayList<Integer>();
        StringBuilder keysOrder = new StringBuilder();
        Set<Integer> keySet = new HashSet<Integer>();
        for (BufferedImageWarp image : imageWarps) {
            int[] rgb = image.getBufferedImage().getRGB(0, 0, width, height, null, 0, width);
            if (image.isKey()) {
                keyOrderList.add(order);
                int x = (order % 4) * 200;
                int y = order < 4 ? 0 : 200;
                keySet.add(order);
                keysOrder.append(order).append("(").append(x).append(",").append(y).append(")|");
            }
            if (order < 4) {
                destImage.setRGB(x1, 0, width, height, rgb, 0, width);
                x1 += width;
            } else {
                destImage.setRGB(x2, height, width, height, rgb, 0, width);
                x2 += width;
            }
            order++;
        }
        keysOrder.deleteCharAt(keysOrder.length() - 1);
        System.out.println("答案的位置:" + keysOrder);
        String fileName = UUID.randomUUID().toString().replaceAll("-", "");
        String fileUrl=request.getServletContext().getRealPath("assets/daan/")+File.separator+fileName+".jpg";
        saveImage(destImage, fileUrl, "jpeg");

        ImageResult ir = new ImageResult();
        ir.setName(fileName + ".jpg");
        ir.setKeySet(keySet);
        ir.setUniqueKey(fileName);
        ir.setTip(tip);
        return ir;
    }

    private static boolean saveImage(BufferedImage destImage, String fileUrl, String format) throws IOException {
        File file = new File(fileUrl);
        return ImageIO.write(destImage, format, file);
    }


}
```
后台控制类：
```
@Controller
public class LoginController{
    @RequestMapping("/identify")
    public String identify(Model model, ServletResponse response,ServletRequest request) {
        try {

            ImageResult ir = Image.generateImage(request);
            model.addAttribute("file", ir.getName());
            model.addAttribute("tip", ir.getTip());
            Cache.put(ir.getUniqueKey(), ir);
            Cookie cookie = new Cookie("note", ir.getUniqueKey());
            ((HttpServletResponse) response).addCookie(cookie);
        } catch (IOException e) {

        }
        return "login";
    }

    @RequestMapping("/login")
    @ResponseBody
    public String login(String location, ServletRequest request, String userName, String password) {
        Cookie[] cookies = ((HttpServletRequest) request).getCookies();
        Cookie note = null;
        for (Cookie cookie : cookies) {
            if(cookie.getName().equals("note")){
                note = cookie;
                break;
            }
        }
        if (null == note) {
            return "ERROR";
        }
        ImageResult ir = Cache.get(note.getValue());
        Cache.remove(note.getName());
        if (null == location || "".equals(location)) {
            return "ERROR";
        }
        if (validate(location, ir)) {
            return "OK";
        }
        return "ERROR";
    }

    private boolean validate(String locationString, ImageResult imageResult) {

        String[] resultArray = locationString.split(";");
        int[][] array = new int[resultArray.length][2];
        for (int i = 0; i < resultArray.length; i++) {
            String[] temp = resultArray[i].split(",");
            array[i][0] = Integer.parseInt(temp[0]) + 150 - 10;
            array[i][1] = Integer.parseInt(temp[1]) + 300;
        }

        for (int i = 0; i < array.length; i++) {
            int location = location(array[i][1], array[i][0]);
            System.out.println("解析后的坐标序号：" + location);
            if (!imageResult.getKeySet().contains(location)) {
                return false;
            }
        }
        return true;
    }

    private static int location(int x, int y) {
        if (y >= 0 && y < 75) {
            return xLocation(x);
        } else if (y >= 75 && y <= 150) {
            return xLocation(x) + 4;
        } else {
            // 脏数据
            return -1;
        }
    }

    private static int xLocation(int x) {
        if (x >= 0 && x < 75) {
            return 0;
        } else if (x >= 75 && x < 150) {
            return 1;
        } else if (x >= 150 && x < 225) {
            return 2;
        } else if (x >= 225 && x <= 300) {
            return 3;
        } else {
            // 脏数据
            return -1;
        }
    }
}
```
结果演示：<br>
![](https://itmanmzt.github.io/styles/images/picture-code/001.gif){:align="center"}<br><br>

控制台打印信息：<br>
![](https://itmanmzt.github.io/styles/images/picture-code/001.jpg){:align="center"}<br><br>

  <br>

<br>

<center>有Marin的地方就有你的收获</center>