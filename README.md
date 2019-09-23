# CityLearn
CityLearn is  an open source OpenAI Gym environment for the implementation of reinforcement learning in demand response applications.
The environment receives the heating and cooling loads of multiple buildings, as well as a weather file, as inputs. It contains multiple Python classes that can model the energy supply devices (i.e., heat pumps), and the energy storage devices (i.e., batteries, chilled water tanks, or hot water tanks). These classes are assigned to specific buildings, and then controlled by the reinforcement learning agents that the user must provide.
We also provide an example of a reinforcement learning implementation. In particular, we implement the deep deterministic policy gradient algorithm (DDPG) to control 10 buildings under the climatic conditions of Austin, TX. In our example, the price of electicity increases proportionally with the total demand for electricity of the 10 buildings, which rises the cost of electricity for each building. The buildings must learn how to coordinate in such a way that they don't only reduce the consumption of electricity, but also the amount of electricity that they all consume simultaneously. This example is provided as a jupyter notebook file: 'main'.

![Demand-response](https://github.com/intelligent-environments-lab/CityLearn/blob/master/dr.jpg)

*The files "model.py", "agents.py", and "common.py" are dependencies used in our example of the reinforcement learning implementation. They are authored by Maxim Lapan and can be found at: https://github.com/Shmuma/ptan, a repository which is part of the book "Deep Reinforcement Learning Hands-On: Apply modern RL methods, with deep Q-networks, value iteration, policy gradients, TRPO, AlphaGo Zero and more", Packt Publishing, 2018 - by Maxim Lapan.

# Buildings
The heating and cooling demands for the buildings are obtained from CitySim, a building energy simulator for urban scale analysis. CitySim uses geometrical 3D and physical reduced order models to estimate the heating and cooling loads in the buildings. It accounts for the internal heat gains due to solar irradiance, activities of the occupants, and the thermal losses. We modelled the buildings as a single-thermal zones that are provided with various heating and cooling supply devices, and energy storage devices. Every building is instantiated by defining its associated energy supply and storage devices.

![Demand-response](https://github.com/intelligent-environments-lab/CityLearn/blob/master/agents.jpg)

# Heat pumps
Every time step of the simulation, the heating and cooling coefficients of performance (COP) of the heat pump are calculated as a function of the outdoor temperature (T_outdoorAir) from the weather file, the technical efficiency of the heat pump (η), and the target temperatures of the heat/cooling energy sink (T_target), which are specified by the user. Assuming an air-to-water heat pump, T_target is approximately the temperature of the water to which the heating or cooling energy is being supplied.
The methods get_max_cooling_power() and get_max_heating_power() compute the maximum amount of heating or cooling that the heat pump can provide based on its nominal power of the compressor and its COP. Finally, the methods get_electric_consumption_cooling() and get_electric_consumption_heating() return the amount of electricity consumed by the heat pump for a given amount of supplied heating or cooling energy.
# Energy storage
The EnergyStorage class has a method to charge and discharge the storage device and track its state-of-charge (SOC), and the energy balance (how much energy it takes from the heat pump and how much it delivers to the building). These method accounts for the efficiency, possible thermal losses coefficient (to be used in thermal storage devices), and limits of operation in terms of nominal power and capacity. Calling the method charge(self, energy) with energy < 0 will discharge energy from the energy storage device into the building. Furthermore, CityLearn allows defining a single instance of the storage device for multiple instances of the class Building, with the objective of having a group of buildings sharing a same energy storage device.
# Additional functions
In addition to the methods of each class, we have created two additional functions to help the users in the process of building the environment.
The function building_loader(demand_file, weather_file, buildings) receives a dictionary with all the building instances and their respectives IDs, and loads them with the data of heating and cooling loads from the simulations.
The function auto_size(buildings, t_target_heating, t_target_cooling) can be used to size automatically all the energy supply and storage devices. This function assumes fixed target temperatures of the heat pump for heating and cooling, which combines with weather data to estimate their COP every hour for the whole simulation period. These estimates are used to size the heat pump in order to guarantee that it will always be able to fully satisfy the heating and cooling demands of the building. This function also sizes the energy storage devices based on heuristic methods.
