有间隔地拼接图片
`
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;

public class HandleImg {
    private static final Logger logger = LoggerFactory.getLogger(HandleImg.class);
    public static void main(String[] args) throws Exception {
        String filePath = "D:\\AAfile\\";
        String path = filePath + "test.jpg";
        String targetFile=filePath+"out.jpg";
        //先横向拼接
        hanleImg(path,  1, filePath+"temp.jpg");
        //再纵向拼接
        hanleImg(filePath+"temp.jpg",  2, targetFile);

    }

    public static void hanleImg( String path, int type, String targetFile) throws IOException {

        File file = new File(path);

        //多张图片的拼接
        int len = 3;
        int XSPACE = 8*(len+1);     //x方向间隔数
        int YSPACE = 8*(len+1);     //y方向间隔数
        BufferedImage[] images = new BufferedImage[len];
        for(int i = 0; i < len; i++)
            images[i] = ImageIO.read(file);

        int[][] ImageArrays = new int[len][];

        for (int i = 0; i < len; i++) {
            int width = images[i].getWidth();
            int height = images[i].getHeight();
            ImageArrays[i] = new int[width * height];
            ImageArrays[i] = images[i].getRGB(0, 0, width, height, ImageArrays[i], 0, width);
        }
        int newHeight = 0;
        int newWidth = 0;
        for (int i = 0; i < images.length; i++) {
            // 横向
            if (type == 1) {
                newHeight = newHeight > images[i].getHeight() ? newHeight : images[i].getHeight();
                newWidth += images[i].getWidth();
            } else if (type == 2) {// 纵向
                newWidth = newWidth > images[i].getWidth() ? newWidth : images[i].getWidth();
                newHeight += images[i].getHeight();
            }
        }
        if (type == 1 && newWidth < 1) {
            return;
        }
        if (type == 2 && newHeight < 1) {
            return;
        }
        // 生成新图片
        try {
            int height_i = 0;
            int width_i = 0;

            if (type == 1) {
                BufferedImage ImageNew = new BufferedImage(newWidth+XSPACE, newHeight, BufferedImage.TYPE_INT_RGB);
                ImageNew.getGraphics().setColor(Color.white);
                for (int i = 0; i < images.length; i++) {
                    ImageNew.setRGB(width_i+8, 0, images[i].getWidth(), newHeight, ImageArrays[i], 0,
                            images[i].getWidth());
                    width_i += images[i].getWidth()+8;
                }
                ImageIO.write(ImageNew, targetFile.split("\\.")[1], new File(targetFile));
                logger.info("拼接图片成功！");
            } else if (type == 2) {
                BufferedImage ImageNew = new BufferedImage(newWidth, newHeight+YSPACE, BufferedImage.TYPE_INT_RGB);
                ImageNew.getGraphics().setColor(Color.white);
                for (int i = 0; i < images.length; i++) {
                    ImageNew.setRGB(0, height_i+8, newWidth, images[i].getHeight(), ImageArrays[i], 0, images[i].getWidth());
                    height_i += images[i].getHeight()+8;
                }
                ImageIO.write(ImageNew, targetFile.split("\\.")[1], new File(targetFile));
                logger.info("拼接图片成功！");
            }

        } catch (Exception e) {
            logger.error("拼接图片失败:{}", e);
        }
    }

}
`
效果图
![输入图片说明](pic/Picture-interval-splicing-1.jpg)
![输入图片说明](pic/Picture-interval-splicing-2.jpg)
