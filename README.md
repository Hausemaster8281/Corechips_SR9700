# Corechips_SR9700
The 10/100 Ethernet adapter made by corechips does not have a proper driver/usb_modeswitch technique(cumbersome) for linux. Hardware changes are made to the adapter for functionality.

# The Problem
1. Lack of linux drivers
2. USB Mode-switch requirement
The ethernet adapter initially shows up as a storage device(Interface 0), and has to be set to ethernet mode(Interface 1) by the driver.

# No consistent software solutions have been found for years.
Before anything, massive credits to everyone who clarified the software side of things. The software side helped me understand the overall working and limitations of this device.

# The hardware mod
1. Open up your Corechips SR9700 adapter, and you'll get to see something along the lines of ![SR9700](https://github.com/user-attachments/assets/04af63a3-9e4a-4b9c-aa8a-441f3ef09573)
2. Here, 2 ICs are seen, the one with the SR9700 marking is your ethernet controller
3. The second eight-terminal IC is our point of interest - ![SR9700a](https://github.com/user-attachments/assets/56c513f1-faec-4683-846b-ae537f061764)
4. This IC is responsible for storing the drivers which show up during the mass-storage mode of the adapter
5. Upon investigating the 2 ICs further, it was seen that the 8-terminal IC is a "4M-bit Serial Peripheral Interface NOR Flash"
6. This led me to believe that the CoreChips IC was setup to initially mount this as a loop device
7. When trigerreed by the driver, the chip would stop the SPI interface and switch to Ethernet Interface
8. This led to a test where the SPI communications were interrupted by shorting the **CS** and **SO** pin on the SPI Flash - ![SR9700b](https://github.com/user-attachments/assets/d5f0dceb-a02f-4767-b0b9-19dc069135ad)
9. Connecting the device with the legs shorted led to the Corechips IC jumping directly to the ethernet adapter mode <img width="612" height="52" alt="Was_Supposed_To_Do_PLA" src="https://github.com/user-attachments/assets/f51b0d05-d58d-4316-adb2-39e8eac62d77" />
10. This led to curiosity on how the IC would handle the absence of the IC
11. I brute-forced the IC joints, and connected the adapter
12. And the ethernet adapter bypassed USB mass storage mode and proceeded to the same mode as in **9**
13. In conclusion, you may short the **CS** and **SO** pins, or **physically remove the SPI flash** to completely bypass the mode-switching drivers

# Conclusion
While a simpler solution on the software side would be useful for the end-user, this was the relatively easy method in our case due to the sheer lack of information of the special mode switching parameters or drivers from the manufacturer.
