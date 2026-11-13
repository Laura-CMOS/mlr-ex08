REPORT for EXERCISE 8 



1. How I Tested

I started with the basic leaf-detecting model and tested new training tricks one by one. To see how it would actually perform on a real phone, I tested it on standard validation photos and a messy "camera-like" dataset where images were randomly cropped, rotated, and color-shifted.



2. What Worked Best

Randomizing colors was the biggest single win. It stopped the network from just relying on lighting or background colors and forced it to learn the actual shapes of the leaves, boosting real-world accuracy by 9%. Another massive, easy upgrade was saving the model at its peak performance during training rather than keeping the final version at the very end. By combining these color changes with geometric tweaks, I bumped the model's accuracy on camera-like data from a weak 62% up to almost 82%.




3. What Didn't Work

Not everything I tried was successful. Rotating and cropping images without also changing the colors actually confused the model and made it worse. Similarly, freezing the main part of the network to only train the final classification layer resulted in lower accuracy in real-world conditions.




4. What Goes on the Phone

The optimized model is exported as task4/leaf_model_best.onnx. It retains the original MobileNet architecture, 96 × 96 input size, and 6.3 MB footprint, ensuring identical mobile inference speeds. The live application intentionally continues to run the older Task 2 model to keep the review and screenshots completely accurate to the current build. Updating the app requires only a single file replacement, which would instantly increase real-world camera accuracy from 62% to 82%.




5. What Needs Fixing Next

Moving forward, I need to fix the app's overconfidence, as it currently shows 100% certainty even when it guesses wrong. I also need to test how the model handles leaves that appear very small in the camera frame, since that is how users typically hold their phones.
