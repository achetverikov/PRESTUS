# Tips on running the thermal simulations
- The thermal simulations themselves are based on the output of the acoustic simulations.
- So running the thermal simulations without any acoustic output to base it on is not an option.

Before feeding in parameters for the heating simulations it is important to note that these simulations don't model the individual pulses and their pulse-repetition frequencies (PRF) but concatenate the pulses over a trail into one block of stimulation and one block without.

# Example

Let's take a PRF of 5Hz, a stimulation duration (`stim_duration` in a configuration file) of 1s, an inter-trial-interval (ITI) of 15s (`iti`), 30 trials (`n_trials`) and a duty cycle of 30% (`duty_cycle`, 0.3).

When running the actual stimulation, these are the parameters of the stimulation sequence the transducer will produce:
- PRF frequency of 5Hz means that each pulse repetion period is 200 ms (1/5 of a second)
- Within each pulse repetition period, the pulse parameters are determined by the duty cycle (0.3), the transduer will be 'on' for 60ms (0.3*200 ms) of stimulation, and 'off' for 140ms (0.7*200 ms) without stimulation.
- Stimulation will occur 5 times (5 pulse repetition periods) within your stimuation duration of 1s.
- After this, no stimulation will be administered for 14s.
- This cycle will be repeated 30 times.

For the simulations, the number of pressure changes is reduced to limit the computational load.

When 'equal_steps' is enabled, the amount of simulated steps is reduced according to the 'sim_time_steps' duration. For example, when using a 'sim_time_steps' duration of 10 ms, 6 steps of stimulation and 14 steps without stimulation are simulated 5 times for each stim_duration (20*5 = 100 steps).

When 'equal_steps' is off, every on and off step will be simulated as one simulation step (2*5 = 10 steps), reducing the computational load.

# Parameters explanation
## Parameters that have to be changed for each experiment
- duty_cycle has to be a value between 0 and 1, and represents the percentage of time of the trial during which stimulation is administered.
- iti is the inter-trial-interval given in seconds
- n_trials is representative of the aboumt of trials that your experiment will use.
- Controls the amount of times the wrapper has to loop through the k-wave thermal simulation.
- stim_duration refers to the length of time in seconds in a trial during which stimulation is adminstered.

## Parameters that should be changed with caution
These parameters influence the underlying mechanisms of how the thermal simulations are run.

- sim_time_steps refer to the duration of each simulated step, so the length of the loop.
- on_off_step_duration is the duration of each duty cycle.
- equal_steps, when enabled, will concatenate different steps to reach a simulation duration similar to your duty cycle. If disabled, it will try to compute the step duration necessary for your given duty cycle.
- post_stim_time_step_dur refers to the amount of steps in between two trials.

Not meeting the following assumptions can prevent the thermal simulations from running:
- on_off_step_duration * duty_cycle / sim_time_steps should produce an integer.
- on_off_step_duration * (1 - duty_cycle) / sim_time_steps should produce an integer.
- cycle_duration / on_off_step_duration should produce an integer.

## Optional parameters
- temp_0 refers to the starting temperature in degrees.
- sensory_xy_halfsize refers to the window along the transducer axis in which temperature changed are recorded.
- record_t_at_every_step can be enabled if there is a need to record temperature changes at every timestep, but can be very memory intensive.
