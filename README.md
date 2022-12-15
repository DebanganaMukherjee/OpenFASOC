# OpenFASOC Flow

OpenFASoC is a project focused on automated analog generation from user specification to GDSII with fully open-sourced tools. It is led by a team of researchers at the University of Michigan and is inspired from FASoC which sits on proprietary software.
The goal of the FaSoC generator is to reduce the number of manual/custom steps and use Automated Digital design flow even for analog designs.
Learn more about OpenFasoc at: https://github.com/idea-fasoc/OpenFASOC
Learn more about FaSoC at: https://fasoc.engin.umich.edu/

## Phase Locked Loop(PLL)
### Working and Block diagram

A Phase locked loop(PLL) mainly consists of the following four blocks:-

1. Phase Detector(PD)
2. Charge Pump (CP)
3. Voltage Controlled Oscillator (VCO)
4. Frequency Divider (FD)

1. Phase Detector:- Phase detector produces two DC voltages namely UP and DOWN, which is proportional to the phase difference between the input signal(Vref) and feedback (output) signal(Vout). If the Vref phase is lagging with respect to Vout then UP signal remains high to the duration of their phase difference and the DOWN signal remains low. If the Vref phase is leading with respect to Vout then DOWN signal remains high to the duration of their phase difference and the UP signal remains low. The Phase detector is constructed using two negative edge triggered D Flip-Flops and a AND gate, which makes it a digital block.

![image](https://user-images.githubusercontent.com/110731913/207903203-8cb0a10c-cf2a-47ff-a43a-39b41a9e1a75.png)

Lagging Condition shown below:-


![image](https://user-images.githubusercontent.com/110731913/207903275-26b462f3-8265-4216-ad1d-4fce93b2309a.png)


2. Charge Pump:- Charge Pump is used to convert the digital measure of the phase difference done in the Phase Detector into a analog control signal, which is used to control the Voltage Controlled Oscillator in the next stage. In the construction of the Charge Pump we use a current steering model which makes it a Analog block. Here when the UP signal is high the current flows from Vdd to output which charges the load capacitance. When the DOWN signal is high the current flows from load capacitance to ground which is discharging of the current.


![image](https://user-images.githubusercontent.com/110731913/207903349-ff8a7041-7eb1-41fd-bca5-118bf3f96a60.png)

Avg Active time of UP   > Avg Active time of DOWN = Charging of Capacitance     [0-->1]--> Speeds up the VCO
Avg Active time of DOWN > Avg Active time of UP   = Dis-Charging of Capacitance [1-->0]--> Slows down the VCO


![image](https://user-images.githubusercontent.com/110731913/207903630-64963f54-4d8b-4d8a-83df-d55ad52a2739.png)

3. Voltage Controlled Oscillator:- The Output of the Charge Pump acts as a Control signal to the Voltage Controlled Oscillator.The VCO generates a DC signal, the amplitude of which is proportional to the amplitude of output of Charge Pump Control Signal. Here the adjustment in the output frequency/phase of VCO is made until it shows equivalency with the input signal frequency/phase. The VCO is contructed using two current mirrors and a ring Oscillator which makes it a Analog block. The control signal is used as an input to these current source(mirrors) to control the current supply which in turn control the delay of the circuit. By controlling the delay we are basically controlling the frequency of the Oscillator which makes it frequency flexible.


![image](https://user-images.githubusercontent.com/110731913/207903711-f532c72e-cc76-4f9e-8a98-293c72be5ac8.png)


4. Frequency Divider:- Frequency Divider is used to divide the frequency which is otherwise a multiplier in time of the Output voltage from the Voltage Controlled Oscillator and is feedback as an input to the Phase Detector, which is then compared with the Vref input signal in the Phase Detector stage. The Frequency Divider is constructed using a series of Toggle Flip-Flops, which makes it a complete Digital block.

![image](https://user-images.githubusercontent.com/110731913/207903905-667ffb9e-15c4-40fd-beeb-eff1c1388e6e.png)

## PLL Generator Flow: Verilog File Generation

- Open the terminal in the PLL-gen directory and execute make sky130hd_pll_verilog to generate the Verilog files.
- Running make sky130hd_pll_verilog executes the PLL-gen.py script from PLL-gen/tools/. 
- This file takes the input specifications from test.json and outputs Verilog files containing the description of the circuit.
- PLL-gen.py calls other modules from PLL-gen/tools/ during execution. For example, parameter.py is in charge of reading test.json, checking for correct user input and choosing the correct circuit elements for the PLL circuit.

