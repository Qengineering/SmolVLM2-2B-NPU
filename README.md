# SmolVLM2-2.2B NPU
![Alt text](https://github.com/user-attachments/assets/6d297a34-c516-4cb1-be4a-bca471d40fa6)
<br><br>**User**:\<image\>Describe the image.<br><br>
**Answer**: In this captivating digital painting, an astronaut is comfortably seated on the surface of the moon, with their feet propped up on a green cooler. The astronaut, clad in a pristine white spacesuit, has a relaxed posture, suggesting they are enjoying a leisurely moment away from the hustle and bustle of space exploration.

The backdrop to this tranquil scene is a breathtaking view of Earth, which is visible in the distance. The planet's blue oceans contrast beautifully with its brown landmasses, while its white clouds add depth to the atmosphere. The moon itself forms a stark, cratered landscape that serves as an excellent contrast to the vibrant colors of Earth.

The astronaut's gaze is directed towards the Earth, indicating their fascination with our home planet. Their relaxed demeanor and the presence of the cooler suggest they might be taking a break from their spacewalk or perhaps enjoying a well-deserved moment on the lunar surface. The painting beautifully captures this serene moment in space exploration, highlighting both the astronaut's peaceful solitude and the awe-inspiring beauty of Earth.

------------

## SmolVLM2-2.2B VLM for RK3588 NPU (Rock 5, Orange Pi 5). <br/>
[![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://opensource.org/licenses/BSD-3-Clause)<br/><br/>
Paper: https://huggingface.co/blog/smolvlm2
Hugging face: https://huggingface.co/blog/smolvlm2

------------

## Introduction

LLMs (Large Language Models) are neural networks trained on large text datasets to understand and generate language.<br>
VLMs (Vision-Language Models) add a visual encoder, enabling the model to process images and text together.<br> 
A combined VLM+LLM system is often referred to as a multimodal model.

These models can be large—hundreds of millions to billions of parameters—which impacts accuracy, memory use, and runtime speed.<br>
On edge devices like the RK3588, available RAM and compute are limited, and even the NPU has strict constraints on supported operations.<br>
Because of this, models typically need to be quantised or simplified to fit.

Performance is usually expressed in tokens (words) per second.<br>
Once converted to RKNN, parts of the model can run on the NPU, improving speed.<br>
Despite these limits, models like SmolVLM2-2B run well on the RK3588 because the NPU efficiently accelerates the heavy math, and the vision encoder can be optimised. This makes advanced multimodal AI feasible on small, power-efficient devices.

------------

## Model performance benchmark (FPS)

All models, with C++ examples, can be found on the Q-engineering GitHub.<br><br>
All LLM models are quantized to **w8a8**, while the VLM vision encoders use **fp16**.<br>

| model         | RAM (GB)<sup>1</sup> | llm cold sec<sup>2</sup> | llm warm sec<sup>3</sup> | vlm cold sec<sup>2</sup> | vlm warm sec<sup>3</sup> | Resolution | Tokens/s |
| --------------| :--: | :-----: | :-----: | :--------: | :-----: | :--------:  | :--------: |
| [Qwen2](https://github.com/Qengineering/Qwen2-NPU)         | 3.3 | 29.1 |   2.5 | 17.1  | 1.7 | 392 x 392 | 12.5 |
| [InternVL3-1B](https://github.com/Qengineering/InternVL3-NPU)  | 1.3 |  6.8 |   1.1 | 7.8    | 0.75 | 448 x 448 | 30 |
| [SmolVLM2-2.2B](https://github.com/Qengineering/SmolVLM2-2B-NPU) | 3.4 | 21.2 |   2.6 | 10.5   | 0.9  | 384 x 384 | 11 |
| [SmolVLM2-500M](https://github.com/Qengineering/SmolVLM2-500M-NPU) | 0.8 |  4.8 |   0.7 | 2.5    | 0.25 | 384 x 384 | 31 |
| [SmolVLM2-256M](https://github.com/Qengineering/SmolVLM2-256M-NPU) | 0.5 |  1.1 |   0.4 | 2.5    | 0.25 | 384 x 384 | 54 |

<sup>1</sup> The total used memory; LLM plus the VLM. <br>
<sup>2</sup> When an llm/vlm model is loaded for the first time from your disk to RAM or NPU, it is called a cold start.<br>
The duration depends on your OS, I/O transfer rate, and memory mapping.<br> 
<sup>3</sup> Subsequent loading (warm start) takes advantage of the already mapped data in RAM. Mostly, only a few pointers need to be restored.<br><br>
<img width="600" height="450" alt="vlm_memory_vs_mmmu" src="https://github.com/user-attachments/assets/a6f0df78-fee8-4e19-9e30-aef9305af670" /><br>


------------

## Dependencies.
To run the application, you have to:
- OpenCV 64-bit installed.
- rkllm library.
- rknn library.
- Optional: Code::Blocks. (```$ sudo apt-get install codeblocks```)

### Installing the dependencies.
Start with the usual 
```
$ sudo apt-get update 
$ sudo apt-get upgrade
$ sudo apt-get install cmake wget curl
```
#### OpenCV
To install OpenCV on your SBC, follow the Raspberry Pi 4 [guide](https://qengineering.eu/install-opencv-on-raspberry-64-os.html).<br><br>
Or, when you have no intentions to program code:
```
$ sudo apt-get install lib-opencv-dev 
```
------------

## Installing the app.
```
$ git clone https://github.com/Qengineering/SmolVLM2-2B-NPU.git
```

#### RKLLM, RKNN
To run SmolVLM2-2B, you need to have the **rkllm-runtime** library version **1.2.2** (or higher) installed, as well as the **rknpu driver** version **0.9.8**.<br>
If you don't have these on your machine, or if you have a lower version, you need to install them.<br>
We have provided the correct versions in the repo.<br>
```bash
$ cd ./SmolVLM2-2B-NPU/aarch64/library
$ sudo cp ./*.so /usr/local/lib
$ cd ./SmolVLM2-2B-NPU/aarch64/include
$ sudo cp ./*.h /usr/local/include
```
### Download the LLM and VLM model.
The next step is downloading the models.<br>
Download the two files (1.5 GB) from our Sync.com server:<br>
[smolvlm2-2.2b-instruct_w8a8_rk3588.rkllm](https://ln5.sync.com/dl/2ac529d30#49g27fih-qe8mjmu9-prmas7px-uuyua5te) and [smolvlm2-2.2b_vision_fp16_rk3588.rknn](https://ln5.sync.com/dl/b565d2360#wmbmdbum-tk36pehc-5t4irskd-7gb6kfti)<br>
Copy both to your `./model` folder.

### Building the app.
Once you have the two models, it is time to build your application.<br>
You can use **Code::Blocks**.
- Load the project file *.cbp in Code::Blocks.
- Select _Release_, not Debug.
- Compile and run with F9.
- You can alter command line arguments with _Project -> Set programs arguments..._ 

Or use **Cmake**.
```
$ mkdir build
$ cd build
$ cmake ..
$ make -j4
```
### Running the app.
The app has the following arguments.
```bash
VLM_NPU Picture RKNN_model RKLLM_model NewTokens ContextLength
```
| Argument   | Comment |
| --------------| --  |
| picture | The image. Provide a dummy if you don't want to use an image | 
| RKNN_model | The visual encoder model (vlm) | 
| RKLLM_model | The large language model (llm) | 
| NewTokens | This sets the maximum number of new tokens. Optional, default 2048| 
| ContextLength | This specifies the maximum total number of tokens the model can process. Optional, default 4096| 

<br>In the context of the Rockchip RK3588 LLM (Large Language Model) library, the parameters NewTokens and ContextLength both control different limits for text generation, and they're typical in LLM workflows.<br>
**NewTokens**<br> 
This sets the maximum number of tokens (pieces of text, typically sub-word units) that the model is allowed to generate in response to a prompt during a single inference round. For example, if set to 300, the model will not return more than 300 tokens as output, regardless of the prompt length. It's important for controlling generation length to avoid too-short or too-long responses, helping manage resource use and output size.<br>
**ContextLength**<br>
This specifies the maximum total number of tokens the model can process in one go, which includes both the prompt (input) tokens and all generated tokens. For example, if set to 2048 and your prompt already uses 500 tokens, the model can generate up to 2048-500 = 1548 new tokens. This is a hardware and architecture constraint set during model conversion and deployment, as the context window cannot exceed the model's design limit (for instance, 4096 or 8192 tokens depending on the model variant).

A typical command line can be:
```bash
VLM_NPU ./Moon.jpg ./models/SmolVLM2-2B-1b_vision_fp16_rk3588.rknn ./models/SmolVLM2-2B-1b_w8a8_rk3588.rkllm 2048 4096
```
The NewTokens (2048) and ContextLength (4096) are optional and can be omitted.
### Using the app.
Using the application is simple. Once you provide the image and the models, you can ask everything you want.<br>
Remember, we are on a bare Rock5C, so don't expect the same quality answers as ChatGPT can provide.<br>
On the other hand, when you see the examples below, the app performs amazingly well.<br><br>
If you want to talk about the picture, you need to include the token `<image>` in your prompt once.<br>
The app remembers the dialogue until you give the token `<clear>`.<br>
With `<exit>`, you leave the application.
### C++ code.  
Below, you find the surprisingly little code of main.cpp. 
```cpp
#include "RK35llm.h"

int main(int argc, char** argv)
{
    std::string input_str;
    std::string output_str;
    RK35llm RKLLM;

    RKLLM.SetInfo(true);            //yes, you may give me additional model information
    RKLLM.SetSilence(false);        //you may print the incremental text chunks on the terminal

    if     (argc< 4) {std::cerr << "Usage: " << argv[0] << " image vlm_model llm_model [option]NewTokens [option]ContextLength\n"; return -1;}
    else if(argc==4) RKLLM.LoadModel(argv[2],argv[3]);
    else if(argc==5) RKLLM.LoadModel(argv[2],argv[3],std::atoi(argv[4]));
    else if(argc> 5) RKLLM.LoadModel(argv[2],argv[3],std::atoi(argv[4]),std::atoi(argv[5]));

    cv::Mat Pic = cv::imread(argv[1]);
    RKLLM.LoadImage(Pic);

    while(true) {
        printf("\n");
        printf("User: ");

        std::getline(std::cin, input_str);
        if (input_str == "exit") break;

        output_str = RKLLM.Ask(input_str);
//        std::cout << "\nLLM Reply: " << output_str << std::endl;
    }

    return 0;
}
```
Most code speaks for itself. One remark.<br>
The LLM generates the answer to your request in little pieces of text. You see them as if the LLM is typing on your terminal.<br>
RKLLM.SetSilence controls this behaviour. When set to `true`, it will suppress this output. RKLLM.Ask() also returns the complete answer.<br>
In main.cpp uncomment this line:
```cpp
std::cout << "\nLLM Answer: " << output_str << std::endl;
```

------------

## VLM examples.
| Image         | Dialog |
| --------------| --  |
| ![Singapore_S](https://github.com/user-attachments/assets/e742059a-3bdb-41be-9647-e7767da12ce6) |**User**: \<image\>Which city is this?<br>**Answer**: The image you've shared appears to be of the Singapore skyline at dusk, with the iconic Marina Bay Sands hotel and the Singapore Flyer Ferris wheel prominently featured. The colors in the photo are warm and inviting, with a mix of orange, yellow, and blue hues reflecting off the water's surface. This time-lapse effect gives the impression that the city is alive and bustling even though it's just dusk.<br<<br>Singapore is known for its modern architecture, vibrant culture, and diverse population. The Marina Bay Sands hotel is a luxury resort complex located in the heart of Singapore, featuring three interconnected towers with a distinctive shape inspired by the shape of a sail. The Singapore Flyer Ferris wheel, on the other hand, is one of the tallest Ferris wheels in the world, offering panoramic views of the city skyline and the surrounding waterfront.<br><br>Singapore's economy is driven by its strong financial sector, with major industries including manufacturing, services, and tourism. The country has also made significant strides in areas like technology, healthcare, and education. Its strategic location at the tip of the Malay Peninsula makes it a hub for trade and commerce between Asia and Europe.<br><br>Overall, the image captures the essence of Singapore's modern cityscape, blending natural beauty with human-made marvels to create a unique urban landscape. | 
| ![ChineseWall_S](https://github.com/user-attachments/assets/3ba4b57a-4b22-4050-b995-9599c6d239de) |**User**: \<image\>Tell me something about the history of this place.<br>**Answer**: The Great Wall of China, also known as the "Ming Great Wall," is one of the most iconic and historically significant structures in the world. It stretches over 4,000 miles (6,400 km) across northern China, from Shanhai Pass in the east to Mutianyu in the west. The wall was built during the Ming Dynasty (1368-1644), which is why it's often referred to as the "Ming Great Wall."<br><br>The construction of the wall began around 200 BCE and continued for over 500 years, with various rulers adding to its length and strength. The primary purpose of the wall was to protect China from invasions by nomadic tribes from Central Asia. However, it also served as a symbol of Chinese power and unity, and played a significant role in the country's military strategy during the Ming Dynasty.<br><br>The wall is made up of stone, brick, tamped earth, and wood, with some sections featuring watchtowers and beacon towers that could be used to communicate over long distances. The wall was built using local materials, such as limestone, sandstone, and granite, which were quarried from nearby mountains.<br><br>Over time, the wall has undergone several renovations and restorations, with many sections being rebuilt or repaired during different periods of Chinese history. Despite its age, the Great Wall remains an impressive feat of engineering and a testament to China's rich cultural heritage.<br><br>Today, the Great Wall is one of the most popular tourist destinations in China, attracting millions of visitors each year. It's also a UNESCO World Heritage Site, recognized for its cultural and historical significance. The wall has been featured in numerous films, books, and other media, cementing its place as an iconic symbol of Chinese culture and history.|
| ![Pizza_S](https://github.com/user-attachments/assets/26fd5da3-f491-4b48-978a-14eae0819247) |**User**: \<image\>What are the ingredients I need to make this?<br>**Answer**: To create this delicious pizza, you will need:<br>1. **Pizza Dough**: You can use a homemade or store-bought pizza dough for this recipe. Make sure it's freshly baked and has a golden crust.<br>2. **Tomato Sauce**: Use a high-quality tomato sauce that is made with fresh tomatoes and herbs. Avoid using canned sauces as they often contain added preservatives.<br>3. **Mozzarella Cheese**: Fresh mozzarella cheese is the best choice for this pizza. You can use either fresh mozzarella or low-moisture mozzarella if you prefer a crisper crust.<br>4. **Basil Leaves**: Fresh basil leaves are essential for adding flavor and aroma to your pizza. You can also use dried basil if fresh is not available.<br>5. **Olive Oil**: A drizzle of olive oil will enhance the flavors of your pizza. It's best to use extra virgin olive oil for its high-quality flavor.<br>6. **Salt and Pepper**: To taste, season your pizza with salt and pepper to balance out the flavors.<br>Optional ingredients:<br>- **Pepperoni or Sausage**: Add some meatiness to your pizza by using pepperoni or sausage slices.<br>- **Onions**: Thinly sliced onions can add a sweet and savory flavor.<br>- **Garlic**: Minced garlic can be added for an extra layer of flavor.<br>- **Cheese Grits**: A sprinkle of cheese grits can provide a crunchy texture and additional flavor.<br><br>Remember, the key to making a great pizza is using high-quality ingredients and letting them meld together in the oven. Enjoy your homemade pizza!<br><br>**User**: What is the first step?<br>**Answer**: The first step to making this delicious pizza is to prepare the pizza dough. You can either make your own from scratch or use a store-bought pizza dough. If you're using store-bought, follow the instructions on the packaging for how to warm it up before baking.<br>Here's a simple recipe for homemade pizza dough:<br><br>Ingredients:<br>- 2 cups of warm water<br>- 1 packet (or 1/4 cup) of active dry yeast<br>- 3 tablespoons of sugar<br>- 4 cups of all-purpose flour<br>- 1 teaspoon of salt<br>- 1 tablespoon of olive oil<br><br>Instructions:<br>1. In a large mixing bowl, combine the warm water and yeast. Let it sit for about 5 minutes until the mixture becomes frothy.<br>2. Add the sugar to the yeast mixture and stir well.<br>3. Gradually add in the flour, one cup at a time, until the dough comes together in a ball.<br>4. Knead the dough on a floured surface for about 10 minutes until it becomes smooth and elastic.<br>5. Place the dough in a greased bowl, cover it with plastic wrap or a damp cloth, and let it rise in a warm place for about 1 hour, or until it has doubled in size.<br>6. Preheat your oven to 425°F (220°C) with a baking stone or baking sheet inside if you have one.<br>7. Punch down the dough and shape it into your desired pizza shape.<br>8. Place the pizza on a baking sheet lined with parchment paper and bake for about 12-15 minutes, or until the crust is golden brown and the cheese is melted and bubbly.<br><br>Once you've prepared the dough, you can start adding your favorite toppings!|
| ![Titanic_S](https://github.com/user-attachments/assets/bf6da76c-c9a4-41c6-bea1-2b4411dd4013) |**User**: \<image\>Which movie is this?<br>**Answer**: This image appears to be from the movie "The Prestige" directed by Christopher Nolan, which was released in 2006. The scene shows two characters standing on a boat or ship during sunset with their arms outstretched as if they are embracing each other. This pose is often associated with romantic gestures and can symbolize love, connection, or the idea of being lost at sea. The image captures the essence of the film's themes of obsession, rivalry, and the lengths people will go to achieve their goals.|

## LLM example.
Notice the nice Markdown makeup of the answer!<br><br>
**User**: Can you make me a C++ call that embeds the Pythagorean theorem?<br><br>
**Answer**: Here's a simple C++ function that calculates the length of the hypotenuse (the longest side) in a right triangle using the Pythagorean theorem:
```cpp
#include <iostream>
using namespace std;

double calculateHypotenuse(double a, double b) {
    return sqrt(a * a + b * b);
}

int main() {
    double sideA = 3.0; // example values for the sides of the triangle
    double sideB = 4.0;
    double hypotenuse = calculateHypotenuse(sideA, sideB);
    cout << "The length of the hypotenuse is: " << hypotenuse << endl;
    return 0;
}
```
This function takes two arguments `a` and `b`, which represent the lengths of the other two sides of the triangle. It returns the square root of the sum of the squares of these two sides, which is the length of the hypotenuse. In this example, we're using it to find the length of the hypotenuse when one side has a length of 3 and the other has a length of 4.
Note that this function assumes that the input values are valid (i.e., they represent positive lengths). If you want to add error checking or validation, you can modify the function accordingly.

------------

[![paypal](https://qengineering.eu/images/TipJarSmall4.png)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=CPZTM5BB3FCYL) 


------------
## Appendix.
Porting the vision module of SmolVLM2 to rknn requires modifications to the intermediate ONNX file.<br>
Specifically, the indices for the Gather operation must be converted from a floating-point (FP16) data type to an integer. Please refer to the following image for an example.<br>


<img width="325" height="285" alt="Screenshot from 2025-10-28 10-19-46" src="https://github.com/user-attachments/assets/e52075bd-b800-4fac-92b9-feea5f1e18fb" />
<img width="325" height="285" alt="Screenshot from 2025-10-28 10-20-45" src="https://github.com/user-attachments/assets/621ec0ba-f5d7-451b-beb2-3a6dc2cdf08c" />
