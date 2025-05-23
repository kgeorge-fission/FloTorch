<script setup lang="ts">
const props = withDefaults(
  defineProps<{
    results?: Record<string, any>[];
    buttonLabel: string;
    questionMetrics?: boolean;
  }>(),
  {
    results: () => [],
    buttonLabel: "Download Results",
    questionMetrics : false
  }
);

const downloadResults = () => {
  const stringifyData = props.results.map((item) => {
    const eval_metrics = item.eval_metrics?.M
    const total_time = item.total_time * 60
    if (props.questionMetrics) {
      const assessments = {
        "guardrail user query assessment":
          JSON.stringify(item.guardrail_input_assessment) || "-",
        "guardrail context assessment":
          JSON.stringify(item.guardrail_context_assessment) || "-",
        "guardrail model response assessment":
          JSON.stringify(item.guardrail_output_assessment) || "-",
      };
      delete item["guardrail_input_assessment"];
      delete item["guardrail_context_assessment"];
      delete item["guardrail_output_assessment"];
      const { id, ...rest } = item;
      return {
        ...rest,
        ...assessments,
      };
    } else {
      const results = {
        id: item.id,
        status: item.experiment_status,
        inferencing_model: item.config.retrieval_model,
        estimated_cost: item.cost || (item.cost === 0 ? 0 : "NA"),
        faithfulness: item.eval_metrics?.M?.faithfulness_score ||item.eval_metrics?.faithfulness_score || (item.eval_metrics?.M?.faithfulness_score === 0 ? 0 : "-"),
        context_precision:
        eval_metrics?.M?.context_precision_score || eval_metrics?.context_precision_score || (eval_metrics?.M?.context_precision_score === 0 ? 0 : "-"),
        maliciousness:
          item.eval_metrics?.M?.aspect_critic_score ||item.eval_metrics?.aspect_critic_score || (item.eval_metrics?.M?.aspect_critic_score === 0 ? 0 : "-"),
        answer_relevance:
          item.eval_metrics?.M?.answers_relevancy_score ||item.eval_metrics?.answers_relevancy_score || (item.eval_metrics?.M?.answers_relevancy_score === 0 ? 0 : "-"),
        duration: item.total_time || (item.total_time === 0 ? 0 : "-"),
        embedding_model: item.config.embedding_model || "NA",
        evaluation_service: item.config.eval_service,
        evaluation_embedding_model:
          item.config.eval_embedding_model,
        evaluation_inference_model:
          item.config.eval_retrieval_model,
        directional_cost: item.config.directional_pricing || (item.config.directional_pricing === 0 ? 0 : "NA"),
        indexing_algorithm: item.config.indexing_algorithm || "NA",
        chunking: item.config.chunking_strategy || "NA",
        inferencing_model_temperature: item.config.temp_retrieval_llm || (item.config.temp_retrieval_llm === 0 ? 0 : "NA"),
        reranking_model: item.config.reranking_model_id || "NA",
        guardrail: item.config?.guardrail_name || "NA",
        bedrock_kb_name: item.config?.kb_name || "NA",
        knn: item.config?.knn_num || (item.config?.knn_num === 0 ? 0 : "NA"),
        n_shots_prompts: item.config.n_shot_prompts || (item.config.n_shot_prompts === 0 ? 0 : "NA"),
        expert_evaluation_scores: item.scores || (item.scores === 0 ? 0 : "NA"),
      }

      return results;
    }
  });
  if (!props.questionMetrics) {
    stringifyData.sort((a, b) => a.id.localeCompare(b.id));
  }
  const csv = jsonToCsv(stringifyData);
  const blob = new Blob([csv], { type: "text/csv" });
  const url = URL.createObjectURL(blob);
  const a = document.createElement("a");
  a.href = url;
  a.download = "results.csv";
  a.click();
};
</script>

<template>
  <UButton
    :label="buttonLabel"
    icon="i-lucide-download"
    @click="downloadResults"
    class="primary-btn"
  />
</template>