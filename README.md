### Simulating #CoronaVirus spread

__IMPORTANT:__ Before you go any further, and especially before you take any real world decisions based on this, understand that this is a toy simulation. I __DO NOT__ know what I am doing. I am __NOT__ and epidemiologist. I was just curious and reading about simulations in a completely different context. I am releasing the code in case someone who does know something about epidemiology wants to tweak the parameters, or adapt the code to do something actually useful. It is very unlikely that without much more informed setting of parameters and/or introduction of more complexity/variables into the model, this will reflect the real world in any useful way. The code is also likely buggy -- it was written hastily, out of curiosity, and has not been tested in any rigorous way. Again, COVID-19 is a serious public health issue - do not rely on this for anything consequential. Please!



Ok! That said, this is a simple simulation of COVID-19 spread. The model makes some basic assumptions and allows the user to simulate a variety of scenarios. The basic idea is to simulate interaction patterns between people in a population to see how effective 'social distancing' can be in fighting the disease.

First we specify the size of the population and the mean number of interactions the average person has. Interactions are assumed to follow a normal distribution. i.e. if the mean is 50, then the average person interacts with 50 people in a day, while some people can interact with as low as 1 person a day and on the high end, people might interact with 100 (or, in a very few cases more than 100 -- 3 standard deviations away from the mean).

So each person is represented by a data structure that stores the following characteristics: their average interactions per day, whether they have the virus, and if they do, when they were infected (we also keep track of each individual interaction for the day).

To begin with all individuals are disease free. We randomly select an individual and introduce the disease. This is day 1.

After this, we simulate each day by drawing random pairs of individuals from this pool. If one of them is infected and the other is not, there is a chance the disease might be transmitted (this probability may be changed -- by default it is 1%). After going through all interactions for a day, we count how many people caught the disease.

The virus has a latency period (default 14, but adjustable) when symptoms aren't visible. During this period people can continue to spread the disease (from the day after they caught it to the day they start showing symptoms). Once they start showing symptoms they are moved out of the population pool into a sick pool. Thence forth we can compute a probability that a person will need to be hospitalized, or moved to an ICU, or even that a person might die.

In terms of support infrastructure, the number of hospital and ICU beds available seem crucial to fighting the disease. So I built those factors into the simulation. Default values for 10000 people are 100 hospital beds and 10 ICU beds -- these are based off [this link](https://en.wikipedia.org/wiki/List_of_countries_by_hospital_beds#2020_coronavirus_pandemic_and_hospital_bed_capacity) and might need modification. If hospital and ICU availability meets the demand, then nothing changes. However, if they start running short, the probablities for escalation are incresed (default factor of 4). In other words, if a person neeeding to be hospitalized can't be hospitalized because no beds are available, the chance that their condition will worsen to the point they will need ICU care increses. Similarly, someone needing ICU care has a greater chance of dying if they can't get it. I just plucked 4 as a factor that seems reasonable. But someone might plug in a better number based on actual data.

In terms of time to complete, unfortunately, since this goes thru _every_ interaction without any sampling, the time complexity seems to be in the order of O(n!) -- in other words adding more people to the population increses the time exponentially. Populations in the order of thousands are easily managed. Tens of thousands seems doable. Beyond that will need some serious computing power or parallelization. Obviously, I only spent a few hours on this and didn't do any of that.

Have fun playing around with this. And stay safe.

Send me suggestions at __prime.lens@gmail.com__


    usage: corona_sim.py [-h] [-d MAX_DAYS_TO_SIMULATE] [-bh BEDS_HOSPITAL]
                         [-bi BEDS_ICU] [-vl VIRUS_LATENT] [-va VIRUS_ACTIVE]
                         [-pt P_TRANSMISSION] [-ph HOSPITAL_PERCENTAGE]
                         [-pi ICU_PERCENTAGE] [-dp DEATH_PERCENTAGE]
                         [-m SHORTAGE_MULTIPLIER] [-o OUTPUT_FILE]
                         n_population mean_interactions

    positional arguments:
      n_population          Size of the population
      mean_interactions     Avg number of interactions for each individual per day

    optional arguments:
      -h, --help            show this help message and exit
      -d MAX_DAYS_TO_SIMULATE, --max_days_to_simulate MAX_DAYS_TO_SIMULATE
                            We'll stop if everyone is healthy before then
                            (default: 365)
      -bh BEDS_HOSPITAL, --beds_hospital BEDS_HOSPITAL
                            Number of hospital beds (default: 500)
      -bi BEDS_ICU, --beds_icu BEDS_ICU
                            Number of ICU beds (default: 50)
      -vl VIRUS_LATENT, --virus_latent VIRUS_LATENT
                            Days virus is latent (no symptoms) (default: 14)
      -va VIRUS_ACTIVE, --virus_active VIRUS_ACTIVE
                            Days virus is active after latency and before recovery
                            (default: 14)
      -pt P_TRANSMISSION, --p_transmission P_TRANSMISSION
                            Probability that an interaction will result in
                            transmission (default: 0.01)
      -ph HOSPITAL_PERCENTAGE, --hospital_percentage HOSPITAL_PERCENTAGE
                            Probability that an infected person will need
                            hospitalization (default: 0.2)
      -pi ICU_PERCENTAGE, --icu_percentage ICU_PERCENTAGE
                            Probability that an infected person will need
                            hospitalization (default: 0.05)
      -dp DEATH_PERCENTAGE, --death_percentage DEATH_PERCENTAGE
                            Probability that an infected person will die (default:
                            0.02)
      -m SHORTAGE_MULTIPLIER, --shortage_multiplier SHORTAGE_MULTIPLIER
                            Chance of escalation if someone needing hospital/ICU
                            can't get it (default: 4)
      -o OUTPUT_FILE, --output_file OUTPUT_FILE
                            Output filename (no extension - autogenerated if not
                            supplied. If it begins with _ then used as suffix
                            (default: None)
