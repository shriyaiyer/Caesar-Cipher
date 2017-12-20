# Caesar-Cipher
Encrypts and Decrypts text using the Caesar cipher
public class Caesar {
    public static void main(String[] args) {
        
        String function = args[0]; // Establishing values for args[0], args[1], 
        // and args[2]
        String filename = args[1];
        // int key = Integer.parseInt(args[2]);
        In inStream = new In(filename); 
        String message = inStream.readAll(); // inStream will read text from a 
        // file
        String dict = args[2];

        if ((args[0]).equals("crack"))
            System.out.println(crack(message, dict)); // carrying out crack 
        // function   
    }
    
    /*
     * Description: converts a string to a symbol array,
     *              where each element of the array is an
     *              integer encoding of the corresponding
     *              element of the string.
     * Input:  the message text to be converted
     * Output: integer encoding of the message
     */
    
    public static int [] stringToSymbolArray(String str) {
        int[] arr = new int[str.length()]; 
        str = str.toUpperCase(); // converting to upper case
        for (int i = 0; i < str.length(); i++) {
            char letter = str.charAt(i); // finding the ith character
            int ourSymbolRepresentation = (int) letter - 'A';
            arr[i] = ourSymbolRepresentation;
            //System.out.print(arr[i] + " ");
            // convert into array
            //arr[i] = shift(arr[i], 2);
        }
        return arr;
    }
    
    /*
     * Description: converts an array of symbols to a string,
     *              where each element of the array is an
     *              integer encoding of the corresponding
     *              element of the string.
     * Input:  integer encoding of the message
     * Output: the message text
     */
    
    public static String symbolArrayToString(int[] symbols) {
        String str = "";
        for (int i = 0; i < symbols.length; i++) {
            int ourSymbolRepresentation = symbols[i];
            char letter = (char) (ourSymbolRepresentation + 'A');
            str = str + letter; // string concatenation
        }
        return str;
    } 
    
    /*
     * Description:  Shifts symbols down by an offset amount (if symbol
     *               is an english letter between 0 and 25)
     * Input:  Unshifted integer array
     * Output:  Shifted integer array
     */
    
    public static int shift(int symbol, int offset) {
        if ((symbol < 0) || (symbol > 25))
            return symbol;
        return (symbol + offset) % 26;
        
    }
    
    /*
     * Description:  Undoes the shifts done by shift()
     * Input:  Shifted integer array
     * Output:  Unshifted integer array
     */
    
    public static int unshift(int symbol, int offset) {
        if ((symbol < 0) || (symbol > 25))
            return symbol;
        return ((symbol + 26) - offset) % 26;
    }
    
    /*
     * Description:  Encryption function that converts the message into
     *               an array of symbols, shifts each symbol by a given
     *               key, and returns a string version of the encrypted
     *               array
     * Input:  String message
     * Output:  Encrypted string message
     */
    
    public static String encrypt(String message, int key) {
        int[] arr = new int[message.length()];
        message = message.toUpperCase();
        for (int i = 0; i < message.length(); i++) {
            char letter = message.charAt(i);
            int ourSymbolRepresentation = (int) letter - 'A';
            arr[i] = ourSymbolRepresentation;
            //System.out.print(arr[i] + " ");
            // convert into array
            arr[i] = shift(arr[i], key);
        }
        return symbolArrayToString(arr);
    }
    
    /*
     * Description:  Decryption function that converts an encrypted message
     *               into an array of symbols, unshifts each symbol by a given
     *               key, and return a string version of the decrypted symbol
     *               array.
     * Input:  Encrypted string message
     * Output:  Decrypted string message
     */
    
    public static String decrypt(String cipher, int key) {
        int[] arr = new int[cipher.length()];
        cipher = cipher.toUpperCase();
        for (int i = 0; i < cipher.length(); i++) {
            char letter = cipher.charAt(i);
            int ourSymbolRepresentation = (int) letter - 'A';
            arr[i] = ourSymbolRepresentation;
            // convert into array
            arr[i] = unshift(arr[i], key);
        }
        return symbolArrayToString(arr);
    }
    
    /*
     * Description: Function that finds the average frequencies of each letter
     *              in English text
     * Input:  File text (read as a string through inStream)
     * Output:  Array of double frequency values 
     */
    
    public static double[] getDictionaryFrequencies(String x) {
        In inStream = new In(x);
        double [] arr = new double [26];
        for (int i = 0; i < 26; i++) {
            arr [i] = inStream.readDouble();
        }
        return arr;
    }
    
    /*
     * Description:  Function that finds the frequencies of letters in the 
     *               cipher text.
     * Input:  Symbol integer array
     * Output: Double array of frequencies of letters
     */
    
    public static double[] findFrequencies(int [] symbolArr) {
        double [] freq = new double [26];
        for (int j = 0; j < 26; j++) {
            freq[j] = 0;
        }
        for (int i = 0; i < symbolArr.length; i++) {
            int k = symbolArr[i];
            if (k >= 0 && k <= 25) {
                freq[k] = freq[k] + 1;
            }
        }
        for (int i = 0; i < 26; i++) {
            freq[i] = freq[i] / symbolArr.length;
        }
        return freq;
    }
    
    /* Description:  Function that finds the absolute value of the difference
     *               between each letter in the ciphertext frequencies and its 
     *               corresponding letter in the english frequencies.
     * Input:  2 double arrays of the frequencies in the cipher text and in the
     *         english text
     * Output: A double value
    */             
    
    public static double scoreFrequencies(double [] freqs, 
                                          double[] englishFreqs) {
        double sum1 = 0;
        for (int i = 0; i < 26; i++) {
            sum1 = sum1 + Math.abs(freqs[i] - englishFreqs[i]);
        }
        return sum1;
    }
    
    /*
     * Description:  Function that cracks encrypted code with an unknown key
     *               using the frequency score
     * Input:  String (encrypted message) and filename of dictionary frequencies
     * Output: cracked code
     */
    
    public static String crack(String encryptMsg, String dict) {
        double min = 9999999; // setting a large value for min so that we can
        // find the real min
        String correctMsg = "";
        int [] outputSymbol = new int [100];
        double [] dictFreq = new double [26]; // get Dictionary Frequencies
        dictFreq = getDictionaryFrequencies(dict);
        double[] freq = new double [26]; // Testing findfrequencies
        for (int i = 0; i < 26; i++) {
            String outputMsg = decrypt(encryptMsg, i);
            outputSymbol = stringToSymbolArray(outputMsg);
            freq = findFrequencies(outputSymbol);
            double score = scoreFrequencies(freq, dictFreq);
            if (score < min) {
                min = score;
                correctMsg = outputMsg;
            }
        }
        return correctMsg;
    }  
}


