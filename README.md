# PrivFace
We aim to devise a faster privacy-preserving face authentication scheme with the revocability and reusability functions (without time-consuming cryptographic operations). 

## Setup
Our scheme is implemented in **JAVA** using **Android Studio** (Version 3.0.1 or 3.4.2).

## Dataset
3,000 pairs of data of highly compact 160-dimensional vectors that contain rich identity information are used as a test set, which has marked indicating whether a pair of data belongs to the same person.

### Data preprocessing
* Turning negative numbers into positive ones by adding a bias value
* Converting the decimals to integers by expanding multiples

## Read data
```
public String readFromSD(String filename) throws IOException {
        StringBuilder sb = new StringBuilder("");
        if(Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
            filename = Environment.getExternalStorageDirectory().getCanonicalPath() + "/" + filename;
            FileInputStream input = new FileInputStream(filename);
            byte[] temp = new byte[1024];
            int len = 0;
            while ((len = input.read(temp)) > 0) {
                sb.append(new String(temp, 0, len));
            }
            input.close();
        }
        return sb.toString();
    }
  ```
  or
  ```
    public String readFromAssets(String filename) throws IOException{
        String result = "";
        InputStream is = getAssets().open(filename);
        int size = is.available();
        byte[] buffer = new byte[size];
        is.read(buffer);
        result = EncodingUtils.getString(buffer,"UTF-8");
        is.close();
        return result;
    }
 ```
 
## REFERENCES
In addition, in our previous study [1], we have used additively homomorphic encryption and secret sharing to construct a revocable and reusable face authentication scheme without the help of a trusted third party.

[1]Lei J, Pei Q, Liu X, et al. A Practical Privacy-Preserving Face Authentication Scheme with Revocability and Reusability[C]. international conference on algorithms and architectures for parallel processing, 2018: 193-203.
