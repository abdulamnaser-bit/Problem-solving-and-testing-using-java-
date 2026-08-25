import java.io.*;
import java.util.*;

public class Solution {

    static class Line {
        int end;
        int value;

        Line(int end, int value) {
            this.end = end;
            this.value = value;
        }
    }

    public static void main(String[] args) throws Exception {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

        int n = Integer.parseInt(br.readLine().trim());
        String s = br.readLine().trim();

        String doubled = s + s;
        int[] odd = oddManacher(doubled);
        int[] even = evenManacher(doubled);

        @SuppressWarnings("unchecked")
        List<Line>[] increasing = new ArrayList[n];

        @SuppressWarnings("unchecked")
        List<Line>[] decreasing = new ArrayList[n];

        @SuppressWarnings("unchecked")
        List<Line>[] constant = new ArrayList[n];

        for (int i = 0; i < n; i++) {
            increasing[i] = new ArrayList<>();
            decreasing[i] = new ArrayList<>();
            constant[i] = new ArrayList<>();
        }

        // Odd-length palindromes
        for (int center = 0; center < doubled.length(); center++) {
            int radius = odd[center];

            int leftLimit = Math.max(0, center - n + 1);
            int rightLimit = Math.min(n - 1, center);

            int flatStart = center + radius - n;
            int flatEnd = center + 1 - radius;

            addLine(increasing, leftLimit,
                    Math.min(rightLimit, flatStart - 1),
                    2 * (n - center) - 1);

            addLine(constant,
                    Math.max(leftLimit, flatStart),
                    Math.min(rightLimit, flatEnd),
                    2 * radius - 1);

            addLine(decreasing,
                    Math.max(leftLimit, flatEnd + 1),
                    rightLimit,
                    2 * center + 1);
        }

        // Even-length palindromes
        for (int center = 1; center < doubled.length(); center++) {
            int radius = even[center];

            if (radius == 0) {
                continue;
            }

            int leftLimit = Math.max(0, center - n + 1);
            int rightLimit = Math.min(n - 1, center - 1);

            int flatStart = center + radius - n;
            int flatEnd = center - radius;

            addLine(increasing, leftLimit,
                    Math.min(rightLimit, flatStart - 1),
                    2 * (n - center));

            addLine(constant,
                    Math.max(leftLimit, flatStart),
                    Math.min(rightLimit, flatEnd),
                    2 * radius);

            addLine(decreasing,
                    Math.max(leftLimit, flatEnd + 1),
                    rightLimit,
                    2 * center);
        }

        PriorityQueue<Line> incHeap = new PriorityQueue<>(
                (a, b) -> Integer.compare(b.value, a.value)
        );

        PriorityQueue<Line> decHeap = new PriorityQueue<>(
                (a, b) -> Integer.compare(b.value, a.value)
        );

        PriorityQueue<Line> constHeap = new PriorityQueue<>(
                (a, b) -> Integer.compare(b.value, a.value)
        );

        StringBuilder output = new StringBuilder();

        for (int rotation = 0; rotation < n; rotation++) {
            incHeap.addAll(increasing[rotation]);
            decHeap.addAll(decreasing[rotation]);
            constHeap.addAll(constant[rotation]);

            removeExpired(incHeap, rotation);
            removeExpired(decHeap, rotation);
            removeExpired(constHeap, rotation);

            int answer = 1;

            if (!incHeap.isEmpty()) {
                answer = Math.max(answer,
                        2 * rotation + incHeap.peek().value);
            }

            if (!decHeap.isEmpty()) {
                answer = Math.max(answer,
                        -2 * rotation + decHeap.peek().value);
            }

            if (!constHeap.isEmpty()) {
                answer = Math.max(answer, constHeap.peek().value);
            }

            output.append(answer).append('\n');
        }

        System.out.print(output);
    }

    static void addLine(List<Line>[] events, int start, int end, int value) {
        if (start < 0) {
            start = 0;
        }

        if (end >= events.length) {
            end = events.length - 1;
        }

        if (start <= end) {
            events[start].add(new Line(end, value));
        }
    }

    static void removeExpired(PriorityQueue<Line> heap, int position) {
        while (!heap.isEmpty() && heap.peek().end < position) {
            heap.poll();
        }
    }

    static int[] oddManacher(String s) {
        int n = s.length();
        int[] radius = new int[n];

        int left = 0;
        int right = -1;

        for (int i = 0; i < n; i++) {
            int k = (i > right) ? 1 : Math.min(radius[left + right - i], right - i + 1);

            while (i - k >= 0 && i + k < n
                    && s.charAt(i - k) == s.charAt(i + k)) {
                k++;
            }

            radius[i] = k;

            if (i + k - 1 > right) {
                left = i - k + 1;
                right = i + k - 1;
            }
        }

        return radius;
    }

    static int[] evenManacher(String s) {
        int n = s.length();
        int[] radius = new int[n];

        int left = 0;
        int right = -1;

        for (int i = 0; i < n; i++) {
            int k = (i > right) ? 0 : Math.min(radius[left + right - i + 1], right - i + 1);

            while (i - k - 1 >= 0 && i + k < n
                    && s.charAt(i - k - 1) == s.charAt(i + k)) {
                k++;
            }

            radius[i] = k;

            if (i + k - 1 > right) {
                left = i - k;
                right = i + k - 1;
            }
        }

        return radius;
    }
}
