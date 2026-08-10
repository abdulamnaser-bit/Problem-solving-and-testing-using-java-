import java.util.*;

public class Solution {

    static boolean isOdd(int n) {
        return n % 2 != 0;
    }

    static boolean isPrime(int n) {
        if (n < 2)
            return false;
        for (int i = 2; i * i <= n; i++) {
            if (n % i == 0)
                return false;
        }
        return true;
    }

    static boolean isPalindrome(int n) {
        int original = n;
        int rev = 0;

        while (n > 0) {
            rev = rev * 10 + (n % 10);
            n /= 10;
        }

        return original == rev;
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int T = sc.nextInt();

        while (T-- > 0) {
            int ch = sc.nextInt();
            int num = sc.nextInt();

            switch (ch) {
                case 1:
                    System.out.println(isOdd(num) ? "ODD" : "EVEN");
                    break;

                case 2:
                    System.out.println(isPrime(num) ? "PRIME" : "COMPOSITE");
                    break;

                case 3:
                    System.out.println(isPalindrome(num) ? "PALINDROME" : "NOT PALINDROME");
                    break;
            }
        }

        sc.close();
    }
}
