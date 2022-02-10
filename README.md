# SuperGen

The source code for ICML'22 Submission 7706.

## Requirements

Before running, you need to first install the required packages by typing following commands (Using a virtual environment is recommended):

```
pip3 install -r requirements.txt
```

## Generating Training Data

The generated training set used in the paper are provided as `train.json` files under each task directory; you should be able to obtain very similar generated data by following the steps below:

**Data Generation**: The entry script for generating training data for GLUE tasks is [`gen_train_data.py`](gen_train_data.py). The basic usage is
```
python gen_train_data.py --task $TASK --label $LABEL --save_dir $SAVE_DIR --num_gen $NUM_GEN
```
You can generate training data of each label either by setting individual label name `$LABEL` one at a time or by setting `$LABEL=all` to generate data for all labels (this will still be done sequentially). You may want to set `$NUM_GEN` to be larger than the desired training set size, as only those texts with the highest generated probability will be used to form the final training set.

**Data Selection**: After generating the training data, the final training set can be constructed by running the following:
```
python src/gen_utils.py --task $TASK --num_select_samples $NUM_SELECT \
                        --read_dir $SAVE_DIR --save_dir $DATA_DIR
```

**Example**: We provide an example script [`run_gen.sh`](run_gen.sh) that includes the entire generation process for all GLUE tasks under the setting described in the paper.

## Fine-Tuning

The entry script for fine-tuning on generated data is [`finetune.py`](finetune.py). The basic usage is
```
python finetune.py \
    --task_name $TASK \
    --data_dir data/$TASK \
    --overwrite_output_dir \
    --do_train \
    --do_predict \
    --smooth $SM \
    --momentum $MOMENT \
    --eval_steps $INTERVAL \
    --threshold $TH \
    --reg_weight $REG \
    --temp_ensemble_rampup $RAMP \
    --model_name_or_path $MODEL \
    --max_seq_length 128 \
    --first_sent_limit 100 \
    --per_device_train_batch_size $BS \
    --learning_rate $LR \
    --num_train_epochs 3 \
    --output_dir $OUT_DIR \
    --template $TEMPLATE \
    --mapping $MAPPING \
    --warmup_ratio 0.1 \
    --save_at_last \
```

**Example**: We provide an example script [`run_finetune.sh`](run_finetune.sh) with command line arguments set up for all GLUE tasks under the setting described in the paper.
