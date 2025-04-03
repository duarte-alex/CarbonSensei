<h1 align="center">
  <br>
  <img width="300" alt="image" src="https://github.com/user-attachments/assets/0f20755c-fa05-41e7-be3e-e0d8cb08965a" />
  <br>
  Carbon Sensei
  <br>
</h1>

<h4 align="center">"Choose the best GPUs and training time for cost, energy and carbon intensity" - Miyamoto Musashi 2025</h4>

As an ML praticioner you are one click away from reducing the environmental impact of your models and costs. Carbon Sensei
suggests you the best GPUs and training time. As Miyamoto Musashi famously said "You must understand that there is more than one path to the top of the mountain". There are two functionalities:

- Find GPU - is a transparent and modular tool that fetches GPUs from major cloud providers and allows you to filter them based on how important costs, C02 emissions and energy efficiency are for you.
- When to train - our ML models allow you to decide if you should train your model now or in the next hour and offer compreensible analytics on your region energy metrics.

## Find GPU

The gpu comparison tools available often do not disclose exactly from where they pull gpu information and how they process it. Others that do often use static links which can easily get outdated. CarbonSensei uses API documented from major cloud providers and aggregates them with the external metrics energy efficiency and carbon intensity.

### Preprocessing

When preprocessing the data for simplicity sake we exclude the followings:
- non NVIDIA GPUs
- commitments plans
- preemptible
- fractional GPU machines
- TPUs
- Workstation variants (e.g. aimed at professional graphics and design workloads)

### Carbon intensity

The regions shortcode names can be mapped to their corresponding countries or regions. For example, GCP ```europe-west1``` corresponds to Belgium. API such as Electricty Maps allow us to fetch current carbon intensity. Considering some official cloud websites display the average year carbon intensity for their regions we use those values.

### Energy efficiency

Cloud providers do not disclose the energy efficiency associated with their GPUs. There are profiling tools to measure GPU power. However, resource sharing, benchmarking inconsistencies and cloud provider limitations makes it very hard to measure energy efficiency in the cloud. Besides this, system-Level efficiency data is important but very hard to infer. Their performance in well know benchmarks like of Green500 is evaluated for complex and very distinct set ups. 

Considering this, the energy efficiency is calculated using NVIDIA manufacturer details and the relative energy efficiency is validated by comparing with Green500: ```Energy efficiency = PEAK FLOPS/TDP```.
- Peak flops: theoretical maximum but in real-world applications, actual performance often reaches between 75% to 85% of these theoretical peaks
- TDP: maximum amount of heat a system is designed to dissipate under real-world applications

### Set up environment

To contribute to the code or run locally the GPU comparison tool you can use the following conda environment and follow the credentials step up tutorial.
```
conda env create -f environment.yml
conda activate gpu-comparison-tool
```


### Set up credentials

Following each of the sections add your credentials to the .env file:

#### GCP

To be able to query the Compute Engine API and follow best security pratices you need to create a service account. Your application will call Google APIs on behalf of the service account generating an OAuth2 token.

In the Google Cloud Console go to IAM & Admin then Service Accounts and then create a service account. Assign the role Compute Viewer to grant read access to Compute Engine resources. Download the JSON key for the service account and store it in the credentials folder. Add to a .env file:

- export GCP_SERVICE_ACCOUNT_FILE = "credentials/{service_account}.json"
- export GCP_PROJECT_ID = "{gcp_project_id}"

The GCP APIs that are queried are:

- Cloud Billing API: Fetch SKU (Stock Keeping Units) that has information regarding GPU prices
- Compute Engine: Fetch available for every regions


#### AWS

Enable your AWS account for all regions that you are going to query. By the default some regions are not activated. In the IAM Console open the IAM dashboard click Users then add users with the needed permissions. Select AWS access type and check Programmatic access. You should then create an access key and a secret access key that will be used to query the AWS APIs.

- export AWS_SECRET_ACCESS_KEY ="{secret_access_key}"
- export AWS_ACCESS_KEY_ID="{access_key_id}"

### When to Train
