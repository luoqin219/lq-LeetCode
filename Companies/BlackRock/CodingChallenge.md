# Coding Challenge for Program Analyst in Technology - Software Engineering Team

### 1. Calculate the Change

The goal of this question is to design a cash register program. Your register currently has the following notes/coins within it:

•    One Pence: .01

•    Two Pence: .02

•    Five Pence: .05

•    Ten Pence: .10

•    Twenty Pence: .20

•    Fifty Pence: .50

•    One Pound: 1

•    Two Pounds: 2

•    Five Pounds: 5

•    Ten Pounds: 10

•    Twenty Pounds: 20

•    Fifty Pounds: 50

The aim of the program is to calculate the change that has to be returned to the customer with the least number of coins/notes. Note that the expectation is to have an object-oriented solution - think about creating classes for better reusability.

Input：

Your program should read lines of text from standard input (this is already part of the initial template). Each line contains two numbers which are separated by a semicolon. The first is the Purchase price (PP) and the second is the cash(CH) given by the customer.

Output：

For each line of input print a single line to standard output which is the change to be returned to the customer. If CH == PP, print out Zero. If CH > PP, print the amount that needs to be returned (in terms of the currency values). Any other case where the result is an error, the output should be ERROR.

The output should the change from highest to lowest denomination.

#### Implementation

```java
import java.io.IOException;
import java.math.BigDecimal;

public class Solution {
    class Change {
        final BigDecimal[] COINS = new BigDecimal[] {
                new BigDecimal("0.01"),
                new BigDecimal("0.02"),
                new BigDecimal("0.05"),
                new BigDecimal("0.1"),
                new BigDecimal("0.2"),
                new BigDecimal("0.5"),
                new BigDecimal("1"),
                new BigDecimal("2"),
                new BigDecimal("5"),
                new BigDecimal("10"),
                new BigDecimal("20"),
                new BigDecimal("50")};
        final String[] NAMES = new String[] {
                "One Pence", "Two Pence", "Five Pence", "Ten Pence", "Twenty Pence", "Fifty Pence",
                "One Pound", "Two Pounds", "Five Pounds", "Ten Pounds", "Twenty Pounds", "Fifty Pounds"
        };
        BigDecimal amount;
        int[] coinsCnt = new int[COINS.length];

        public Change(BigDecimal amount) {
            this.amount = amount;
        }

        public void getLeastChangeCoins(BigDecimal change) {
            for (int i = COINS.length - 1; i >= 0; i--) {
                if (change.compareTo(BigDecimal.ZERO) > 0) {
                    while (change.compareTo(COINS[i]) >= 0) {
                        change = change.subtract(COINS[i]);
                        coinsCnt[i]++;
                    }
                } else
                    break;
            }
        }

        public void printResult() {
            if (amount.compareTo(BigDecimal.ZERO) == 0) System.out.println("Zero");
            else if (amount.compareTo(BigDecimal.ZERO) < 0) System.out.println("ERROR");
            else {
                getLeastChangeCoins(amount);
                StringBuilder sb = new StringBuilder();
                for (int i = coinsCnt.length - 1; i >= 0; i--) {
                    while (coinsCnt[i] > 0) {
                        sb.append(NAMES[i] + ", ");
                        coinsCnt[i]--;
                    }
                }
                System.out.println(sb.substring(0, sb.length() - 2));
            }
        }
    }

    public static void main(String[] args) throws IOException {
        double price = 10.56;
        double cash = 20;
        Solution sl = new Solution();
        Change money = sl.new Change(new BigDecimal(String.valueOf(cash - price)));
        money.printResult();
    }
}
```

