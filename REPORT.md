REPORT for EXERCISE 8 



1. How I Tested

I started with the basic leaf-detecting model and tested new training tricks one by one. To see how it would actually perform on a real phone, I tested it on standard validation photos and a messy "camera-like" dataset where images were randomly cropped, rotated, and color-shifted.



2. What Worked Best

Randomizing colors was the biggest single win. It stopped the network from just relying on lighting or background colors and forced it to learn the actual shapes of the leaves, boosting real-world accuracy by 9%. Another massive, easy upgrade was saving the model at its peak performance during training rather than keeping the final version at the very end. By combining these color changes with geometric tweaks, I bumped the model's accuracy on camera-like data from a weak 62% up to almost 82%.




3. What Didn't Work

Not everything I tried was successful. Rotating and cropping images without also changing the colors actually confused the model and made it worse. Similarly, freezing the main part of the network to only train the final classification layer resulted in lower accuracy in real-world conditions.




4. What Goes on the Phone

I am exporting this winning combined model to the web app. It is much more accurate now but still lightweight enough to run quickly on a mobile browser.




5. What Needs Fixing Next

Moving forward, I need to fix the app's overconfidence, as it currently shows 100% certainty even when it guesses wrong. I also need to test how the model handles leaves that appear very small in the camera frame, since that is how users typically hold their phones.