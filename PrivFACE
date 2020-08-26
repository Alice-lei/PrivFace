% Android Studio 3.4

import androidx.appcompat.app.AppCompatActivity;
import android.os.Bundle;
import android.app.Activity;
import android.os.Bundle;
import android.os.Environment;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import org.apache.http.util.EncodingUtils;
import android.widget.Toast;

import java.io.InputStream;
import java.io.FileInputStream;
import java.io.IOException;
import java.math.BigDecimal;
import java.math.BigInteger;
import java.util.ArrayList;
import java.util.Random;

public class MainActivity extends Activity {

    //secure parameter p
    //80bit
    //public static final BigInteger P = new BigInteger("734095675167610427486539");
    //128bit
    //public static final BigInteger P = new BigInteger("313338255604920949022028065218431446231");
    //160bit
    public static final BigInteger P = new BigInteger("1248878241464690322159961483223834105349154894079");
    //256bit
    // public static final BigInteger P = new BigInteger("66405879945252276366429021340050544119210103710737018505472676728403367984943");

    //Decimal threshold
    BigDecimal T;
    //Integer threshold
    BigInteger TB;

    String f1 = "f1.txt";
    String f2 = "f2.txt";

    ArrayList<BigInteger> X=new ArrayList<BigInteger>();//Original feature vector
    ArrayList<BigInteger> X_=new ArrayList<BigInteger>();//Query feature vector for authentication

    ArrayList<BigDecimal> orgFaceList=new ArrayList<BigDecimal>();

    ArrayList<BigInteger> A = new ArrayList<BigInteger>();//Random vector
    ArrayList<BigInteger> B = new ArrayList<BigInteger>();//B=X-A=(b1,b2,...,bn) bi=xi-ai
    ArrayList<BigInteger> C = new ArrayList<BigInteger>();//Random vector
    ArrayList<BigInteger> D = new ArrayList<BigInteger>();//D=A-C，di=ai-ci
    ArrayList<BigInteger> E = new ArrayList<BigInteger>();//E=eD,ei=e*di, e is a random number

    BigInteger a,c,e;//Random numbers
    BigInteger b;//b=||X||^2-a=x1^2+x2^2+...+xn^2-a modp
    BigInteger d;//d=b+||X_||^2-2B*X_+2c=b+(x_1^2+...+x_n^2)-2(b1*x_1+...+bn*x_n)+2c modp
    BigInteger f ;//f=C*X_+c=c1*x_1+...+cn*X_n+c,，,modP
    BigInteger g = new BigInteger("0");//g=E*X_=e1*x_1+...+en*x_n modp
    BigInteger s;//s=d-2(f+1/e * g)+a
    BigInteger e_1;//the inverse of e
    BigInteger exponent = new BigInteger("2");//square

    String CompareResult = "";
    private Button verify_btn;
    private Button register_btn;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        register_btn=(Button)findViewById(R.id.register_btn);
        verify_btn=(Button)findViewById(R.id.verify_btn);

        register_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
            
            }
        });

        verify_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                long StartTime = System.currentTimeMillis();
                initFaceData();
                long EndTime = System.currentTimeMillis();
                long Time = EndTime-StartTime;
                String TimeStr = String.valueOf(Time);
               Toast.makeText(getApplicationContext(),"Test Result="+CompareResult+" Time="+TimeStr+"ms"+s,Toast.LENGTH_LONG).show();
               
                X.clear();
                X_.clear();
                A.clear();
                B.clear();
                C.clear();
                D.clear();
                E.clear();
                a = new BigInteger("0");
                b = new BigInteger("0");
                c = new BigInteger("0");
                d = new BigInteger("0");
                e = new BigInteger("0");
                f = new BigInteger("0");
                g = new BigInteger("0");
                s = new BigInteger("0");
                e_1 = new BigInteger("0");
                
              
            }
        });
    }

    //160-dimensional random vector generation
    public ArrayList GRandomVector(){
        String s="0123456789";
        Random rand = new Random();
        ArrayList<BigInteger> A = new ArrayList<BigInteger>();
        
        for(int i=0; i<160; i++) {
            //int r=rand.nextInt(10000000) + 1;
            StringBuffer sb=new StringBuffer();
            for(int j=0;j<15;j++){
                int r=rand.nextInt(10);
                sb.append(s.substring(r,r+1));
            }
            BigInteger bint=new BigInteger(sb.toString());
            A.add(bint);

            // Log.v("test",a.get(i).toString());
        }

        return A;
    }

    //Random number generation
    public BigInteger GRandomData(){
        BigInteger RR;
        String s="0123456789";
        Random rand = new Random();

        StringBuffer sb=new StringBuffer();
        for(int j=0;j<18;j++){
            int r=rand.nextInt(10);
            sb.append(s.substring(r,r+1));
        }
        RR=new BigInteger(sb.toString());
        return RR;
    }

    public void  initVerifyData(){
        A = GRandomVector();
        a = GRandomData();

        //B=X-A
        for(int i = 0;i<160;i++){
            BigInteger t = X.get(i).subtract(A.get(i)).mod(P);
            // BigInteger t = X.get(i).subtract(A.get(i));
            B.add(t);
        }

        //b = ||X||^2-a
        BigInteger X_2 = new BigInteger("0");//X_2 = x1^2+x2^2+....
        for (int i = 0; i <160; i++){
            BigInteger X2 = X.get(i);
            X2 = X2.modPow(exponent,P);//||X||^2
            // X2=X2.pow(2);
            X_2=X_2.add(X2);//x1^2+x2^2+....
        }
        b = X_2.subtract(a).mod(P); //  b = X_2.subtract(a);
       
       c = GRandomData();

        //d = b+||X_||^2-2B*X_+2
        BigInteger X1_2 = new BigInteger("0");//X1_2=x_1^2+x_2^2+....
        for (int i = 0 ; i <160; i++){
            BigInteger X3 = X_.get(i);
            X3 = X3.modPow(exponent,P);
            X1_2 = X1_2.add(X3);
        }
        BigInteger n = new BigInteger("0");
        BigInteger n1 = new BigInteger("0");
        for (int i =0; i<160;i++){
            BigInteger b_= B.get(i);
            BigInteger x_ = X_.get(i);
            b_ = b_.multiply(x_).mod(P);
            n1 = n1.add(b_).mod(P);
        }
        n = n1.multiply(exponent).mod(P);
        BigInteger m = c.multiply(exponent).mod(P);
        d = b.add(X1_2).subtract(n).add(m).mod(P);

        C = GRandomVector();
        e = GRandomData();

        //D = A - C 
        for (int i = 0;i<160;i++){
            BigInteger a1 = A.get(i);
            BigInteger c1 = C.get(i);
            BigInteger d1 = a1.subtract(c1);
            D.add(d1);
        }

        //E = e*D
        for (int i = 0;i<160;i++){
            BigInteger e1 = e.multiply(D.get(i)).mod(P);
            E.add(e1);
        }

        //f = C * X_+c  
        BigInteger p = new BigInteger("0");
        for (int i = 0; i <160;i++){
            BigInteger c2 = C.get(i);
            BigInteger x_1 = X_.get(i);
            c2 = c2.multiply(x_1).mod(P);
            p.add(c2);
        }
        f = p.add(c).mod(P);

        //g= E*X_
        for (int i = 0;i<160;i++){
            BigInteger e1 = E.get(i);
            BigInteger x_2 =X_.get(i);
            BigInteger u = e1.multiply(x_2).mod(P);
            g = g.add(u).mod(P);
        }

        //s = d -2(f+e^-1*g)+a 
        //e_1
        e_1 = e.modInverse(P);
        BigInteger p2 = e_1.multiply(g).mod(P);
        BigInteger p3 = f.add(p2).mod(P);
        BigInteger p4 = exponent.multiply(p3).mod(P);
        s = d.subtract(p4).add(a).mod(P);
    }
/*
    public String readFromSD(String filename) throws IOException {
        StringBuilder sb = new StringBuilder("");
        if(Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
            filename = Environment.getExternalStorageDirectory().getCanonicalPath() + "/" + filename;
            //Open the file input stream
            FileInputStream input = new FileInputStream(filename);
            byte[] temp = new byte[1024];

            int len = 0;
            //Read File:
            while ((len = input.read(temp)) > 0) {
                sb.append(new String(temp, 0, len));
            }
            //Close the input stream
            input.close();
        }
        return sb.toString();
    }
    */
    public String readFromAssets(String filename) throws IOException{
        String result = "";
        // InputStream is = getAssets().open("f9.txt");
        InputStream is = getAssets().open(filename);
        int size = is.available();
        byte[] buffer = new byte[size];
        is.read(buffer);
        result = EncodingUtils.getString(buffer,"UTF-8");
        is.close();
        return result;
    }


    //Data preprocessing
    public void initFaceData(){

        BigDecimal shi=new BigDecimal(10);


       // Log.v("test","------------big int--------------------------");
        try {
           
             //   String f1_str = readFromSD(f1);
             //   String f2_str=readFromSD(f2);
            String f1_str = readFromAssets(f1);
            String f2_str = readFromAssets(f2);


            String[] dote1=f1_str.split(","); 
            for(int i=0;i<dote1.length;i++){
                String dote_item_s=dote1[i];
                BigDecimal b_d=new BigDecimal(dote_item_s);
                BigInteger b_i=b_d.multiply(shi.pow(18)).toBigInteger();
                orgFaceList.add(b_d);
                X.add(b_i);
              //  Log.v("test",b_i.toString());
            }

          //  Log.v("test","--------------------------------------");
            String[] dote2=f2_str.split(",");
            for(int i=0;i<dote2.length;i++){
                String dote_item_s=dote2[i];
                BigDecimal b_d=new BigDecimal(dote_item_s);
                BigInteger b_i=b_d.multiply(shi.pow(18)).toBigInteger();
                X_.add(b_i);
               // Log.v("test",b_i.toString());
            }
        }catch (Exception e){
            e.printStackTrace();
        }

        initVerifyData();

        BigDecimal Ten=new BigDecimal(10);
        T=new BigDecimal(270.575591869);//Threshold in decimal form
        T=T.pow(2);
        //BigDecimal t=new BigDecimal(270.575591869);

        TB=T.multiply(Ten.pow(36)).toBigInteger();//Threshold in integer form

        //Threshold comparison
        int iComResult=s.compareTo(TB);
        if(iComResult>0){
           // Log.v("test","not verified");
            CompareResult = new String("not verified");
        }else{
           // Log.v("test","verified");
            CompareResult = new String("verified");
        }
    }

    public void regist(){
         initFaceData();
    }
    public void veirfiy(){

    }
}

