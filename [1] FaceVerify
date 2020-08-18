%Android Studio 3.0.1

import android.app.Activity;
import android.os.Bundle;
import android.os.Environment;
import android.util.Log;
import android.view.View;
import android.widget.Button;

import java.io.FileInputStream;
import java.io.IOException;
import java.math.BigDecimal;
import java.math.BigInteger;
import java.util.ArrayList;
import java.util.Random;

public class MainActivity extends Activity {

    Paillier paillier = new Paillier();
    BigInteger TB;

    BigDecimal T;
    //BigInteger T;
    //ArrayList<BigInteger> orgFaceList=new ArrayList<BigInteger>();
    //ArrayList<BigInteger> verifyFaceList=new ArrayList<BigInteger>();

    ArrayList<BigDecimal> orgFaceList=new ArrayList<BigDecimal>();
    ArrayList<BigDecimal> verifyFaceList=new ArrayList<BigDecimal>();

    BigInteger RR;
    BigInteger r,p_r,p_rRR;
    ArrayList<BigInteger> X=new ArrayList<BigInteger>(); //Original feature vector
    ArrayList<BigInteger> X_=new ArrayList<BigInteger>(); //Query feature vector for authentication

    ArrayList<BigInteger> a=new ArrayList<BigInteger>();
    ArrayList<BigInteger> p_ra=new ArrayList<BigInteger>();
    ArrayList<BigInteger> Y=new ArrayList<BigInteger>();

    BigInteger z;   //||X||^2+RR
    BigInteger z_;  //||X_||^2
    BigInteger l;   //X_.Y
    BigInteger L;   //||X_||^2+||X||^2+RR-2l

    String f1="face_data/f1.txt";
    String f2="face_data/f2.txt";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button regBtn=(Button)findViewById(R.id.reg_btn);
        Button verifyBtn=(Button)findViewById(R.id.verify_btn);

        regBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

                regist();
            }
        });

        verifyBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                veirfiy();
            }
        });
    }
    private void  initVerifyData(){
        String s="0123456789";
        Random rand = new Random();

        //a
        for(int i=0; i<160; i++) {
            //int r=rand.nextInt(10000000) + 1;
            StringBuffer sb=new StringBuffer();
            for(int j=0;j<15;j++){
                int r=rand.nextInt(10);
                sb.append(s.substring(r,r+1));
            }
            BigInteger bint=new BigInteger(sb.toString());
            a.add(bint);

            Log.v("test",a.get(i).toString());
        }

        //RR
        StringBuffer sb=new StringBuffer();
        for(int j=0;j<18;j++){
            int r=rand.nextInt(10);
            sb.append(s.substring(r,r+1));
        }
        RR=new BigInteger(sb.toString());

        //r
        for(int j=0;j<5;j++){
            int r1=rand.nextInt(10);
            sb.append(s.substring(r1,r1+1));
        }
        r=new BigInteger(sb.toString());

        //Y
        for(int i=0; i<160; i++) {
            //int r=rand.nextInt(10000000) + 1;
            BigInteger t=X.get(i).subtract(a.get(i));
            Y.add(t);
        }

        //z
        BigInteger X_2=new BigInteger("0");
        for(int i=0;i<160;i++) {
            BigInteger X2 = X.get(i);
            X2=X2.pow(2);
            X_2=X_2.add(X2);
        }
        z=X_2.add(RR);

        //z_
        z_=new BigInteger("0");
        for(int i=0;i<160;i++) {
            BigInteger X2 = X_.get(i);
            X2=X2.pow(2);
            z_=z_.add(X2);
        }

        //l
        l=new BigInteger("0");
        for(int i=0;i<160;i++) {
            BigInteger x_ = X_.get(i);
            BigInteger y = Y.get(i);
            x_=x_.multiply(y);
            l=l.add(x_);
        }

        //L
        BigInteger l2=l.add(l);
        L=(z.add(z_)).subtract(l2);


        //p_ra
        for(int i=0; i<160; i++) {

            BigInteger pa1=a.get(i);
            BigInteger pa2=paillier.Encryption(pa1.multiply(r));
            p_ra.add(pa2);
        }

        //p_r
        p_r=paillier.Encryption(r);

        //p_rRR
        p_rRR=paillier.Encryption(r.multiply(RR));

        //pk(ra)^(n-2X_)
        BigInteger t_m=new BigInteger("1");
        for(int i=0;i<160;i++){
            BigInteger p_ra1=p_ra.get(i);
            BigInteger x_1=X_.get(i);
            x_1=paillier.n.subtract(x_1);

            BigInteger t1=p_ra1.modPow(x_1, paillier.nsquare);
            t_m=t_m.multiply(t1).mod(paillier.nsquare);
        }

        //pk(r)^L
        BigInteger t_m1=p_r.modPow(L, paillier.nsquare);

        BigInteger one=new BigInteger("1");
        BigInteger t_m2=p_rRR.modPow(paillier.n.subtract(one), paillier.nsquare);

        BigInteger e_result=t_m.multiply(t_m1).multiply(t_m2);

        BigInteger d_result=paillier.Decryption(e_result);

        d_result=d_result.divide(r);

        int iComResult=d_result.compareTo(TB);
        if(iComResult>0){
            Log.v("test","not verified");
        }else{
            Log.v("test","verified");
        }
    }
    
    private void testPiallier(){

        Paillier paillier = new Paillier();
        /* instantiating two plaintext msgs*/
        BigInteger m1 = new BigInteger("20");
        BigInteger m2 = new BigInteger("60");
        /* encryption*/
        BigInteger em1 = paillier.Encryption(m1);
        BigInteger em2 = paillier.Encryption(m2);
        /* printout encrypted text*/
        System.out.println(em1);
        System.out.println(em2);
        /* printout decrypted text */
        System.out.println(paillier.Decryption(em1).toString());
        System.out.println(paillier.Decryption(em2).toString());

        /* test homomorphic properties -> D(E(m1)*E(m2) mod n^2) = (m1 + m2) mod n */
        BigInteger product_em1em2 = em1.multiply(em2).mod(paillier.nsquare);
        BigInteger sum_m1m2 = m1.add(m2).mod(paillier.n);
        System.out.println("original sum: " + sum_m1m2.toString());
        System.out.println("decrypted sum: " + paillier.Decryption(product_em1em2).toString());

        /* test homomorphic properties -> D(E(m1)^m2 mod n^2) = (m1*m2) mod n */
        BigInteger expo_em1m2 = em1.modPow(m2, paillier.nsquare);
        BigInteger prod_m1m2 = m1.multiply(m2).mod(paillier.n);
        System.out.println("original product: " + prod_m1m2.toString());
        System.out.println("decrypted product: " + paillier.Decryption(expo_em1m2).toString());

    }

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

    private void initFaceData(){

        BigDecimal shi=new BigDecimal(10);
        T=new BigDecimal(270.575591869);//Threshold in decimal form
        T=T.pow(2);
        //BigDecimal t=new BigDecimal(270.575591869);

        TB=T.multiply(shi.pow(36)).toBigInteger();//Threshold in integer form

        Log.v("test","------------big int--------------------------");
        try {
            String f1_str = readFromSD(f1);
            String f2_str=readFromSD(f2);

            String[] dote1=f1_str.split(",");
            for(int i=0;i<dote1.length;i++){
                String dote_item_s=dote1[i];
                BigDecimal b_d=new BigDecimal(dote_item_s);
                BigInteger b_i=b_d.multiply(shi.pow(18)).toBigInteger();
                orgFaceList.add(b_d);
                X.add(b_i);
                Log.v("test",b_i.toString());
            }

            Log.v("test","--------------------------------------");
            String[] dote2=f2_str.split(",");
            for(int i=0;i<dote2.length;i++){
                String dote_item_s=dote2[i];
                BigDecimal b_d=new BigDecimal(dote_item_s);
                BigInteger b_i=b_d.multiply(shi.pow(18)).toBigInteger();
                X_.add(b_i);
                Log.v("test",b_i.toString());
            }
        }catch (Exception e){
            e.printStackTrace();
        }

        initVerifyData();

        /*
        BigInteger t1=new BigInteger("0");
        BigInteger er=new BigInteger("2");
        for(int i=0;i<160;i++){
            BigInteger x1=X_.get(i).multiply(er);
            BigInteger a1=a.get(i).multiply(r);
            t1= t1.add(x1.multiply(a1));
        }
        BigInteger d=(r.multiply(L.subtract(t1))).subtract(r.multiply(RR));
        d=d.divide(r);

        int iComResult=d.compareTo(TB);
        if(iComResult>0){
            Log.v("test","not verified");
        }else{
            Log.v("test","verified");
        }*/

    /*
        BigDecimal sum=new BigDecimal(0.0);
        for(int i=0;i<orgFaceList.size();i++){
            BigDecimal b1=orgFaceList.get(i);
            BigDecimal b2=verifyFaceList.get(i);

            BigDecimal dd=b1.subtract(b2);
            BigDecimal d_2=dd.multiply(dd);
            //BigDecimal d_2=dd.pow(2);
            sum=sum.add(d_2);
        }

        int iComResult=sum.compareTo(T);
        if(iComResult>0){
            Log.v("test","not verified");
        }else{
            Log.v("test","verified");
        }

    */

    }

    private void regist(){
        //initRandomList();
        initFaceData();
    }

    private void veirfiy(){

    }

}
