# tongbu
package tongbu;



import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.io.*;

public class tongbu extends JFrame{
	static String t;
	static String s;
	private JTextField txtTarget;//目标文件夹
    private JTextField txtSource;//源文件夹
    private JLabel lblTarget;
    private JLabel lblSource;
    private JButton btnTarget;//选择源文件夹的按钮
    private JButton btnSource;//选择目标文件夹的按钮
    private JButton btnOk;//开始按钮
    private JButton btnCancel;//取消按钮    
    
    
    public tongbu(){
        this.getContentPane().setLayout(new GridLayout(3,1)); //窗口内布局
        JPanel p1=new JPanel(new FlowLayout(FlowLayout.LEFT));//左对齐
        lblSource=new JLabel("源文件夹    "); //只能看
        txtSource=new JTextField(35);   //文本框
        btnSource=new JButton("浏览...");  //可以按
        
        p1.add(lblSource);
        p1.add(txtSource);
        p1.add(btnSource);
        
        JPanel p2=new JPanel(new FlowLayout(FlowLayout.LEFT));
        lblTarget=new JLabel("目标文件夹");
        txtTarget=new JTextField(35);
        btnTarget=new JButton("浏览...");
        
        p2.add(lblTarget);
        p2.add(txtTarget);
        p2.add(btnTarget);
        
        JPanel p3=new JPanel(new FlowLayout(FlowLayout.RIGHT,20,0));
        btnOk=new JButton("确定");
        btnCancel=new JButton("取消");
        p3.add(btnOk);
        p3.add(btnCancel);
        
        this.add(p1);
        this.add(p2);
        this.add(p3);
        
        this.setTitle("同步");
        this.setResizable(false); //窗口大小是否可以改变
        this.setSize(550,150);
        
        //获得当前屏幕的尺寸
        Dimension screenSize=Toolkit.getDefaultToolkit().getScreenSize();
        //获得当前窗口的尺寸
        Dimension windowSize=this.getSize();
        //窗口居中
        this.setLocation((screenSize.width-windowSize.width)/2,
                            (screenSize.height-windowSize.height)/2);
        
        this.setVisible(true); //窗口是否可见
        ///////////////////////////////////////
        /////          监听部分
        ////////////////////////////////////////
        this.addWindowListener(new WindowAdapter(){    //关闭按钮退出程序
            public void windowClosing(WindowEvent e){
                System.exit(0);
            }
        });
        btnCancel.addActionListener(new ActionListener(){ //取消按钮退出程序
            public void actionPerformed(ActionEvent e){
                System.exit(0);
            }
        });
        btnSource.addActionListener(new ActionListener(){   ////选择源文件夹路径的事件监听：打开文件对话框
            public void actionPerformed(ActionEvent e){
            	
                JFileChooser chooser=new JFileChooser();  
                chooser.setFileSelectionMode(JFileChooser.DIRECTORIES_ONLY );  
                chooser.showDialog(tongbu.this,"源文件夹");  
                File source=chooser.getSelectedFile();
                txtSource.setText(source.getPath());
            }
        });
        btnTarget.addActionListener(new ActionListener(){    //选择目标文件夹路径的事件监听：打开文件对话框
            public void actionPerformed(ActionEvent e){
                
                JFileChooser chooser=new JFileChooser();  
                chooser.setFileSelectionMode(JFileChooser.DIRECTORIES_ONLY );  
                chooser.showDialog(tongbu.this,"目标文件夹");  
                File Target=chooser.getSelectedFile();
                txtTarget.setText(Target.getPath());
                
            }
        });
        btnOk.addActionListener(new ActionListener(){
            public void actionPerformed(ActionEvent e){
                if(!txtSource.getText().trim().equals("")&&!txtTarget.getText().trim().equals("")){
                 //////////////////////////////////////////////
                 //                同步部分
                 //////////////////////////////////////////////
                	s=txtSource.getText();
                	t=txtTarget.getText();
                	tongbu.copyFileOrDir(s,t);
					JOptionPane.showMessageDialog(null,"同步完成");
				 //////////////////////////////////////////////////
                 //
                 /////////////////////////////////////////////////
                }
                else{
                    JOptionPane.showMessageDialog(null,"请输入源文件夹或目标文件夹");
                    return;
                }
            }
        });
    }
    
    
    public static void copyFileOrDir(String srcPath, String targetPath){  
        parseDir(srcPath,targetPath);  
        copyAllFile(srcPath, targetPath);  
    }  
      
    public static void copyAllFile(String srcPath, String targetPath){  
        File src = new File(srcPath); 
        File target = new File(targetPath);
        File srcfilePath=null;
        File targetfilePath=null;
        File[] fileList = src.listFiles(); 
        File[] targetfileList = target.listFiles();
        //将源文件没有的文件删除
        for(File c : targetfileList){
        	String c1 = c.getPath();
        	//将目标文件路径替换到源文件路径
        	srcfilePath = new File(s+c1.replace(t, ""));
        	if (!srcfilePath.exists()) {
        		if(c.isDirectory()){
        			getDelete(c);
        			c.delete();
        		}
        		else{
        			c.delete();
        		}
				System.out.println("删除多余文件");
			}
        }
        for(File f : fileList){ 
        	//将源文件路径替换到复制文件路径
        	String f1 = f.getPath();
        	targetfilePath = new File(t+f1.replace(s, ""));
        	//复制
            if(f.isFile()&&!targetfilePath.exists()) {  
                copyFile(srcPath + "//" + f.getName(), targetPath + "//" + f.getName());  
            } 
            //文件名一样的，且是新文件的就覆盖掉
            if(f.isFile()&&f.lastModified()>targetfilePath.lastModified()) {  
                copyFile(srcPath + "//" + f.getName(), targetPath + "//" + f.getName());  
            }
            //判断是否是目录  
            if(f.isDirectory()) { 
                copyAllFile(f.getPath().toString(), targetPath + "//" + f.getName());  
            }  
        }  
    }  
      
    public static void copyFile(String src, String target){  
            InputStream is = null;  
            OutputStream os = null;  
              
            try {  
                is = new FileInputStream(src);  
                os = new FileOutputStream(target);  
                byte[] buff = new byte[1024];  
                int len = 0;  
                while((len = is.read(buff, 0, buff.length)) != -1) {  
                    os.write(buff, 0, len);  
                }  
                System.out.println("文件拷贝成功！");  
            } catch (FileNotFoundException e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            } catch (IOException e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            } finally {  
                if(os!=null){  
                    try {  
                        os.close();  
                    } catch (IOException e) {  
                        // TODO Auto-generated catch block  
                        e.printStackTrace();  
                    } finally {  
                        if(is!=null){  
                            try {  
                                is.close();  
                            } catch (IOException e) {  
                                // TODO Auto-generated catch block  
                                e.printStackTrace();  
                            }  
                        }  
                    }  
                }  
            }  
              
        }  
      
    public static void parseDir(String pathName, String target){  
        //创建一个新的目录  
        File targetFile = new File(target);  
        if(!targetFile.exists()) {  
            targetFile.mkdirs();  
        }  
          
        //创建一个抽象路径  
        File file = new File(pathName);  
        if(file.isDirectory()){  
            File[] files = file.listFiles();  
            for(File f : files){  
                if(f.isDirectory()) {  
                    parseDir(f.getPath(), target + "//" + f.getName());  
                }  
            }  
        }  
    }
    private static void getDelete(File file) {
        //生成File[]数组   
        File[] files = file.listFiles();
        //判断是否为空   
        if(files!=null){
            for (File file2 : files) {
                //是文件就删除
                if(file2.isFile()){
                    file2.delete();
                }else if(file2.isDirectory()){
                    //是文件夹就递归
                    getDelete(file2);
                    //空文件夹直接删除
                    file2.delete();
                }
            }
        }
        else{
        	file.delete();
        }
        
    }
    public static void main(String[] args) {
		new tongbu();
	}
    
}
	        
	
	
