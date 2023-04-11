# kegcop_home_assistant
Port of Keg Cop to Home Assistant

Here are the steps that I took to build this port of kegcop to home assistant. 

You should have some knowledge of home assistant before starting this project. it is not that hard but it does take some time to learn each step used in this setup.

The first step should be to add the following from the addon store:

1. ESPHome
2. Mariadb
3. Studio code server
4. phpmyadmin (not required but need for testing your sql in mariadb)

![image](https://user-images.githubusercontent.com/18006478/231231123-34866229-236c-4ee7-88ad-a8f03e4cdc4d.png)

Next you should set up mariadb and you config.yaml file.

![image](https://user-images.githubusercontent.com/18006478/231232551-13966eaf-0915-4fb3-9a5f-a5c2a4cc7f60.png)

You will need to get some frontend cards from HACS to for the dashboard:

1. bar-card
2. horizontal-stack
3. mini-graph-card
4. vertical-stack
5. gauge

Check the dashboard YAML for all of the cards that are used.

![image](https://user-images.githubusercontent.com/18006478/231233536-0c225c4e-7218-4e61-aa11-e33daec03632.png)

Install the flowmeter code into the esp32

This is the code that works for me, you will need to test to make sure it works for you type of flow meter:

  - platform: pulse_counter
    state_class: measurement
    name: "count_1"
    id: count_1
    pin: 
      number: GPIO4
      inverted: true
      mode:
        input: true
        pullup: true
    update_interval: 30s
    internal_filter: 13us
    count_mode:
      rising_edge: INCREMENT
      falling_edge: DISABLE
    unit_of_measurement: "pulses"
    accuracy_decimals: 2
    filters:
     - debounce: 0.1s
     - lambda: return (x / 1);  
     


![image](https://user-images.githubusercontent.com/18006478/231233758-487a1834-8616-4218-b0d2-a07ae0db0021.png)

Next you will need to create a date field and a number field within the device and services helpers tab.


![image](https://user-images.githubusercontent.com/18006478/231233964-64242ba4-fc3b-4ae0-8100-e579d777f6f6.png)

I created one each for each keg, here is a date example:

![image](https://user-images.githubusercontent.com/18006478/231234120-f3298b70-ba44-4e18-a36e-c2451879764b.png)

 Here is a number example:

![image](https://user-images.githubusercontent.com/18006478/231234289-f9f20836-82ce-4cc4-a398-f9f751df5748.png)


OPERATION:

The tool is managed in the screen below. There are only three functions that need to be managed.

1. New Keg Date: enter in the date that the keg is tapped. The tool will add up the pulses based on the start date.  Change date when the keg is empty and replaced with a new keg.
2. Keg Size: The keg volume will be based off of the amount selected in the slider.  
3. Pulse per Oz calibration: Pour a measured amount and then change this number to get the correct calculation per pour.  For example, pour 8 oz of liquid and check the control panel results for that pour.  You can hard code your pulse per oz results in the input_numbers.yaml in the initial field. If your home assistant instance restarts it will reset this number back to the initial value.

box1:
  name: Keg 1 PPO calibration
  initial: 27
  min: -20
  max: 135
  step: 1
  mode: box
  

![image](https://user-images.githubusercontent.com/18006478/231262928-adf5e2c3-fcdf-4cff-aae1-2358c28e329b.png)

