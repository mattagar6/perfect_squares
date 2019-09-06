<b>Problem Statement:</b> https://leetcode.com/problems/perfect-squares/

Given a positive integer <b>n</b>, find the least number of perfect square numbers (for example, 1, 4, 9, 16, ...) which sum to <b>n</b>.

    Example 1:

    Input: n = 12
    Output: 3 
    Explanation: 12 = 4 + 4 + 4.
    
    Example 2:

    Input: n = 13
    Output: 2
    Explanation: 13 = 4 + 9.

<b>Solution: Dynamic Programming (C++)</b>

<p>
To solve any problem using Dynamic Programming, there are four main steps we need to complete:
<ol>
<li>Identify the <b>Optimal Substructue</b> of the problem: ie. The solution to a larger problem depends on the solutions to smaller versions of the same problem.
<li>Using the <b>Optimal Substructue</b>, create a <b>Recursive</b> solution to the problem.
<li>Identify the presence of <b>Overlapping Subproblems</b> that will slow down our <b>Recursive</b> solution: ie. our <b>Recursive</b> solution calculates the same value on more than one occasion.
<li>Using some extra memory for the storage of <b>Computed Results</b>, transform our <b>Recursive</b> solution to an <b>Iterative</b> approach.  
</ol>
We will now complete these 4 steps in order to solve the <b>Perfect Squares</b> problem stated above.
</p>

<p>
<ol>
<li><p>Perhaps the most difficult part of this problem is identifying the <b>Optimal Substructue</b>. However, trying to formalize the problem, one may have decided to <b>write out</b> what the optimal solution for some positive integer <b>x</b> would <b>"look like"</b>. Let <b>S<sub>x</sub></b> be the <b>Optimal Sum</b> for <b>x</b> (optimal means the sum that contains the fewest terms), and let <b>a<sub>i</sub></b> be some integer that is squared in <b>S<sub>x</sub></b>. Then,</p> 
<p align="center"><b>S<sub>x</sub> = a<sub>1</sub><sup>2</sup> + a<sub>2</sub><sup>2</sup> + ... + a<sub>n</sub><sup>2</sup></b>, where <b>n</b> is as small as possible.</p>
<p align="center"><b>S<sub>x</sub> = (a<sub>1</sub><sup>2</sup> + a<sub>2</sub><sup>2</sup> + ... + a<sub>n-1</sub><sup>2</sup>) + a<sub>n</sub><sup>2</sup></b></p>
<p>Now, consider the sum inside the parenthesis above. This sum <b>must</b> be the <b>Optimal Sum</b>, <b>S<sub>y</sub></b> for some <b>y < x</b>. Consider what would happen if this were not the case and there were some other sum, <b>S</b>, that contained fewer than <b>n-1</b> terms and summed to <b>y</b>. Then we could simply replace the terms in parenthesis above with <b>S</b> to create a sum for <b>x</b> with fewer than <b>n</b> terms, <b>contradicting</b> the fact that <b>n</b> was chosen to be as small as possible.
<p>Now we know that <b>S<sub>x</sub></b> depends on some <b>S<sub>y</sub></b> where <b>y < x</b>, we can formally define the optimal substructure of the problem. Let <b>dp(x)</b> be the number of terms in the optimal sum for <b>x</b>. Then using <b>x</b> and <b>y</b> from above,</p>
<p align="center"><b>dp(x) = dp(y) + 1</b>, and <b>dp(0) = 0</b> for the base case</p>
<p>However, one small issue remains; we don't know the value for <b>y</b>! No problem, we can simply try out all possibilities and take the value of <b>y</b> with the smallest number of terms in it's optimal sum. The possible values for <b>y</b> are <b>{x - 1<sup>2</sup>, x - 2<sup>2</sup>, ..., x - sqrt(x)<sup>2</sup>}.</b></p>
<li>Using the <b>Optimal Substructue</b> defined above, it is straightforward to come up with a <b>Recursive</b> solution similar to the following:

    int numSquares(int n) {
        if(n == 0) return 0;
        
        int ans = INT_MAX;
        
        for(int i = 1; i <= sqrt(n); i++) {
          ans = min(ans, numSquares(n - i*i) + 1);
        }
        
        return ans;
    }
<li><p>However, the solution above is not efficient. Taking a closer look, it is evident that this approach will call <b>numSquares(x)</b> on the same values of <b>x</b> more than once in our recursive tree. This fact is slowing down our algorithm and will become increasingly apparent for larger values of <b>x</b>; meaning our algorithm doesn't scale well with the size of the input.</p>
<li><p>To remedy the issue of calculating the same values more than once, we will utilize a <b>dp array</b> to store values we have already calculated. <b>dp[i]</b> will represent the minimum number of terms in the optimal sum for <b>i</b> (at the end, we will return <b>dp[n]</b>). This way, rather than traversing the same recursive tree for some value multiple times, we can just calculate each value once and access it directly from the array later.</p>
<p>In order for this strategy to work, we need to compute the values of <b>dp[i]</b> from the <b>bottom-up</b> (ie. calculate smaller values first), rather than the <b>top-down</b> recursive approach. This will produce correct answers becuase of the <b>Optimal Substructure</b> defined in step 1. We can now use two for-loops to fill in increasing values of <b>dp[i]</b> in a similar manner to our recursive approach. Here's the code:</p>
</ol>

</p>

    int numSquares(int n) {
        vector<int> dp(n + 1, 0);

        for(int i = 1; i <= n; i++) {
            dp[i] = INT_MAX;
                
            for(int j = 1; j <= sqrt(i); j++) {
                dp[i] = min(dp[i], 1 + dp[i - j*j]);
            }
        }
        
        return dp[n];
    }

<b>Time Complexity:</b> <b>O(n<sup>3/2</sup>)</b>
<ul>
<li> Our code contains two nested for-loops; the outermost loop excecutes <b>O(n)</b> times, while the inner loop excecutes <b>O(n<sup>1/2</sup>)</b> times. Thus the overall time complexity is <b>O(n<sup>3/2</sup>)</b>.
</ul>
<b>Space Complexity:</b> <b>O(n)</b>
<ul>
<li> Our array <b>dp</b>, which stores the answer for each <b>i</b> in <b>{0, 1, 2, ... , n}</b> takes up <b>O(n)</b> space in memory.
</ul>
