Download Link: https://assignmentchef.com/product/solved-csci-ga-2250-001-programming-assignment-4-lab-4-io-scheduling
<br>
<span style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen-Sans, Ubuntu, Cantarell, 'Helvetica Neue', sans-serif;">You are to implement different IO-schedulers in C or C++ and submit the </span><strong style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen-Sans, Ubuntu, Cantarell, 'Helvetica Neue', sans-serif;">source </strong><span style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen-Sans, Ubuntu, Cantarell, 'Helvetica Neue', sans-serif;">code, which we will compile and run. Your submission must contain a Makefile so we can run on </span><strong style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen-Sans, Ubuntu, Cantarell, 'Helvetica Neue', sans-serif;">linserv*.cims.nyu.edu</strong><span style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen-Sans, Ubuntu, Cantarell, 'Helvetica Neue', sans-serif;"> (and please at least test there as well).</span>




In this lab you will implement/simulate the scheduling of IO operations. Applications submit their IO requests to the IO subsystem, where they are maintained in an IO-queue till the device is ready for issuing another request. The IO-scheduler then selects a request from the IO-queue and submits it to the disk. This is commonly known as the strategy() routine in operating systems. On completion another request can be taken from the IO-queue and submitted to the disk. The scheduling policies will allow for some optimization as to reduce disk head movement or overall wait time in the system. The schedulers to be implemented are FIFO (i), SSTF (j), LOOK (s), CLOOK (c), and FLOOK (f) (the letters in bracket define which parameter must be given in the –s program flag).




Invocation is a follows:

./iosched –s&lt;schedalgo&gt;  [options] &lt;inputfile&gt;




The input file is structured as follows: Lines starting with ‘#’ are comment lines and should be ignored.

Any other line describes an IO operation where the 1<sup>st</sup>  integer is the time step at which the IO operation is issued and the 2<sup>nd</sup> integer is the track that is accesses. Since IO operation latencies are largely dictated by seek delay (i.e. moving the head to the correct track), we ignore rotational and transfer delays for simplicity. The inputs are well formed.




#io generator

#numio=32 maxtracks=512 lambda=10.000000

1 430

129 400 :




We assume that moving the head by one track will cost one time unit. As a result your simulation can/should be done using integers. The disk can only consume/process one IO request at a time. Everything else must be maintained in an IO queue and managed according to the scheduling policy. The initial direction of the LOOK algorithms is from 0-tracks to higher tracks. The head is initially positioned at track=0 at time=0. Note that you do not have to know the maxtrack (think SCAN vs. LOOK).




Each simulation should print information on individual IO requests followed by a SUM line that has computed some statistics of the overall run. (see reference outputs).




For each IO request create an info line (5 requests shown).

0:     1     1   431

1:    87   467   533

2:   280   431   467

3:   321   533   762

4:   505   762   791




Created by

printf(“%5d: %5d %5d %5d
”,i, req-&gt;arrival_time, r-&gt;start_time, r-&gt;end_time);

<ul>

 <li>IO-op#,</li>

 <li>its arrival to the system (same as inputfile)</li>

 <li>its disk start time</li>

 <li>its disk end time</li>

</ul>







<strong>Programming Assignment #4   (Lab 4): IO Scheduling                Professor Hubertus Franke </strong><strong>Class CSCI-GA.2250-001   </strong><strong>Spring 2019 </strong>




and for the complete run a SUM line:




<table width="645">

 <tbody>

  <tr>

   <td width="108">total_time:</td>

   <td width="537">total simulated time, i.e. until the last I/O request has completed.</td>

  </tr>

  <tr>

   <td width="108">tot_movement:</td>

   <td width="537">total number of tracks the head had to be moved</td>

  </tr>

  <tr>

   <td width="108">avg_turnaround:</td>

   <td width="537">average turnaround time per operation from time of submission to time of completion</td>

  </tr>

  <tr>

   <td width="108">avg_waittime:</td>

   <td width="537">average wait time per operation (time from submission to issue of IO request to start disk operation)</td>

  </tr>

  <tr>

   <td width="108">max_waittime:</td>

   <td width="537">maximum wait time for any IO operation.</td>

  </tr>

 </tbody>

</table>




Created by :     printf(“SUM: %d %d %.2lf %.2lf %d
”,                 total_time, tot_movement, avg_turnaround, avg_waittime, max_waittime);




Various sample input and outputs are provided with the assignments on NYU classes.

Please look at the sum results and identify what different characteristics the schedulers exhibit.




You can make the following assumptions:

<ul>

 <li>at most 10000 IO operations will be tested, so its OK (recommended) to first read all requests from file before processing.</li>

 <li>all io-requests are provided in increasing time order (no sort needed)</li>

 <li>you never have two IO requests arrive at the same time (so input is monotonically increasing)</li>

</ul>




I strongly suggest, you don’t use discrete event simulation this time. You can write a loop that increments simulation time by one and checks whether any action is to be taken. In that case you have to check in the following order.

<ul>

 <li>Did a new I/O arrive to the system at this time, if so add to IO-queue ?</li>

 <li>If an IO active and completed at this time, if so compute relevant info and store in IO request for final summary</li>

 <li>If an IO active but did not complete, then move the head by one sector/track/unit in the direction it is going (to simulate seek)</li>

 <li>If no IO request active now (after (2)) but IO requests are pending? Fetch and start a new IO. 5) Increment time by 1</li>

</ul>




When switching queues in FLOOK you always continue in the direction you were going from the current position, until the queue is empty. Then you switch direction until empty and then switch the queues continuing into that direction and so forth. While other variants are possible, I simply chose this one this time though other variants make also perfect sense.




<u>Additional Information:</u>




As usual, I provide some more detailed tracing information to help you overcome problems. Note your code only needs to provide the result line per IO request and the ‘SUM line’.




The reference program under ~frankeh/Public/iosched on the cims machine implements three options –v, -q, -f to debug deeper into IO tracing and IO queues.




The <strong>–v </strong>execution trace contains 3 different operations (<strong><em><u>add</u></em></strong> a request to the IO-queue, <strong><em><u>issue</u></em></strong> an operation to the disk and <strong><em><u>finish</u></em></strong> a disk operation). Following is an example of tracking IO-op 21 through the times 2139..2292 from submission to completion.




2139:    21 add 203                  //  21 is the IO-op # (starting with 0) and 203 is the track# requested

2227:    21 issue 203 268         //  21 is the IO-op #, 203 is the track# requested, 268 is the current track#

2292:    21 finish 65                 //  21 is the IO-op #, 65 is total length of the io from request to completion




<strong>-q</strong> shows the details of the IO queue and direction of movement ( 1==up , -1==down) and  <strong>–f</strong> shows additional queue information during the FLOOK.




Here Queue entries are tuples during add [ ior# : #io-track ] or triplets during get [ ior# : io-track# : distance ],  where distance is negative if it goes into the opposite direction (where applicable ).




Please use these debug flags and the reference program to get more insights on the debugging certain “why” questions.


