import java.security.MessageDigest;
import java.util.ArrayList;
import java.util.Date;

public class FileShareBlockchain {

    public static ArrayList<Block> blockchain = new ArrayList<Block>();  
    public static int difficulty = 5;

    public static void main(String[] args) {
        // Sample blockchain transactions
        blockchain.add(new Block("User1", "User2", "file1.txt")); 
        blockchain.add(new Block("User2", "User3", "file2.pdf"));
    }

    public static class Block {

        public String previousHash; 
        public String fileSender;
        public String fileRecipient; 
        public String file;
        public long timeStamp;  
        public String hash;
        public int nonce;

        public Block(String fileSender, String fileRecipient, String file) {
            this.previousHash = blockchain.size()>0 ? calculateHash(blockchain.get(blockchain.size()-1)) : "";
            this.timeStamp = new Date().getTime();
            this.fileSender = fileSender;
            this.fileRecipient = fileRecipient;
            this.file = file;
            this.hash = calculateHash(); 
        }
        
        public String calculateHash() {
            String calculatedhash = StringUtil.applySha256( 
                previousHash +
                timeStamp +
                fileSender +                   
                fileRecipient +
                file +
                nonce
            );
            return calculatedhash;
        }

        public void mineBlock() {
            String target = new String(new char[difficulty]).replace('\0', '0'); 
            while(!hash.substring( 0, difficulty).equals(target)) {
                nonce ++;
                hash = calculateHash();
            }
        }
    }

}

class StringUtil {
    public static String applySha256(String input){
        
        try {
            MessageDigest digest = MessageDigest.getInstance("SHA-256");
            byte[] hash = digest.digest(input.getBytes("UTF-8"));
            StringBuffer hexString = new StringBuffer();
            for (int i = 0; i < hash.length; i++) {
                String hex = Integer.toHexString(0xff & hash[i]);
                if(hex.length() == 1) hexString.append('0');
                hexString.append(hex);
            }
            return hexString.toString();
        } catch(Exception e) {
            throw new RuntimeException(e);
        }
    }
}
