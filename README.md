# Airavata

[📝 Blogpost](https://ai4bharat.github.io/airavata) | [🤗 HF Model](https://huggingface.co/ai4bharat/airavata) [🤗 HF Dataset](https://huggingface.co/datasets/ai4bharat/indic-instruct-data-v0.1) | [🤗 HF Benchmarks](https://huggingface.co/collections/ai4bharat/airavata-evaluation-suite-65b13b7b68165de71ba0b333)

We release Airavata v0.1, a Hindi chat model instruction finetuned on SarvamAI's OpenHathi. Please refer to our [official blogspot](https://ai4bharat.github.io/airavata/) for our model details, dataset creation and evaluation process.

<p align="center" width="100%">
      <img src="images/airavata_logo.png" alt="Airavta is an Hindi instruction-tuned model based on the IndicInstruct datasets." style="width: 20%; min-width: 200px; display: block; margin: auto;">
</p>

This repo was forked from [allenai/open-instruct](https://github.com/allenai/open-instruct), an open-source initiative for instruction-tuning widely used pretrained language models. More instructions about the codebase can be found there.

## Setup

To run training, evaluation, or inference for our finetuned models, you need to install the required packages by running the following command (after installing pytorch):

```bash
pip install -r requirements.txt
```

If you just want the dependencies for the weight diff script, use:
```bash
pip install -r weight-diff-requirements.txt
```

## Training
In general, any model from the Hugging Face Hub may be loaded for training. You may train a model from scratch or finetune an existing model. The following code snippet shows our command for training SarvamAI's OpenHathi base model.

#### Training a model from scratch

```
# install additional package
pip install lm-dataformat

# preprocess and tokenize the datasets before you perform training from scratch
python3 scripts/tokenize_dataset.py \
    --tokenizer_path <tokenizer_path> \
    --data_path <data_path> \
    --save_path <save_path> \
    --max_seq_length <max_seq_length> \
    --max_examples <max_examples> --max_tokens <max_tokens>

bash scripts/train_with_accelerate.sh
```

#### Finetuning an exisiting model

```
bash scripts/finetune_lora_with_accelerate.sh
```

Please check the scripts for more information on the arguments.

## Dataset Preparation

We cover various instruction datasets to train our chat model. The collection consists of:

* Anudesh
* wikiHow
* Flan v2 (67k sample subset)
* Dolly
* Anthropic-HHH (5k sample subset)
* OpenAssistant v1
* LymSys-Chat (50k sample subset)

We have put together the above datasets and it can be accessed from [Hugging Face](https://huggingface.co/datasets/ai4bharat/indic-instruct-data-v0.1)


```
python3 reformat_indic_instruct_data.py --output_dir <output_dir>
```


## Evaluation

We have evaluated on standard Indic and English benchmarks to assess the capabilities of our model. The benchmarks are:


* Indic NLU and Commonsense Reasoning tasks
    * IndicSentiment
    * IndicCOPA
    * IndicXNLI
    * IndicXParaphrase
* Indic NLG
    * IndicQA
    * IndicHeadlineGeneration
    * IndicWikiBio
* English NLU and Commonsense Reasoning tasks
    * MMLU
    * BoolQ
    * ARC Easy (Both Easy and Challenge subsets)
    * Hellaswag
* English-Hindi Translation
    * Flores
    * IN22-Gen

In addition, we also evaluate on the Indic benchmarks using the translate-test approach ie. evaluating the Hindi language benchmarks by translating it to English on an English model. For this, we use the LLaMA-2 7B chat model. Note that OpenHathi base itself was finetuned on LLaMA-2 7B base, hence it was appropriate to compare our model against its English counterpart.

Similarly, we translate the English benchmarks to Hindi using IndicTrans2 and evaluate them on our model. Note that both OpenHathi and Airavata were trained bilingually on English and Hindi, so they support generation in both languages.

You would have to request access to use the LLaMA variants and log in to the huggingface hub (or pass a token). This process is detailed in the [Hugging Face documentation](https://huggingface.co/docs/transformers/model_doc/llama).

### Example
The evaluation scripts for the benchmarks listed can be found at `scripts/indic_eval/name_of_the_task.sh` for Indic benchmarks and `scripts/translate_test_eval/name_of_the_task.sh` for translate-test. The following command shows how you can evaluate on IndicSentiment
```bash
# Evaluation on IndicSentiment (Hindi) on a 5-shot setting
python3 -m eval.indicsentiment.run_eval \
    --ntrain 5 \
    --save_dir "results/indicsentiment/airavata-5shot" \
    --model_name_or_path ai4bharat/airavata \
    --tokenizer_name_or_path ai4bharat/airavata \
    --eval_batch_size 4

# Evaluation on IndicSentiment (Translate-test) on a 5-shot setting
python3 -m eval.indicsentiment.run_translate_test_eval \
    --ntrain 5 \
    --save_dir "results/translate_test/indicsentiment/llama2-chat-5shot" \
    --model_name_or_path meta-llama/Llama-2-7b-chat-hf \
    --tokenizer_name_or_path meta-llama/Llama-2-7b-chat-hf \
    --eval_batch_size 4
```

## Released Checkpoint(s)

Our chat model is made available on [Hugging Face](https://huggingface.co/ai4bharat/models/airavata).

As for the amount of parameters, LLaMA-2 7B was used to train the OpenHathi (and subsequently Airavata), hence it comprises of **7 billion parameters** in total.


## Citation

If you used this repository or our models, please cite our work:

```bibtex
@misc{airavata2024,
  title = {Introducing Airavata: Hindi Instruction-tuned Chat Model},
  url = {https://ai4bharat.github.io/airavata},
  author = {Jay Gala and Thanmay Jayakumar and Jaavid Aktar Husain and Aswanth Kumar and Mohammed Safi Ur Rahman Khan and Diptesh Kanojia and Ratish Puduppully and Mitesh Khapra and Raj Dabre and Rudra Murthy and Anoop Kunchukuttan},
  month = {January},
  year = {2024}
}
```
