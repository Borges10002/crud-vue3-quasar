<template>
  <q-page padding>
    <q-table title="Artigos" :rows="posts" :columns="columns" row-key="name">
      <template v-slot:top>
        <q-space />
        <q-btn color="primary" label="novo" :to="{ name: 'formPost' }" />
      </template>
      <template v-slot:body-cell-actions="props">
        <q-td :props="props" class="q-gutter-sm">
          <q-btn
            icon="edit"
            color="info"
            dense
            size="sm"
            @click="handleEditarPost(props.row.id)"
          />
          <q-btn
            icon="delete"
            color="negative"
            dense
            size="sm"
            @click="handleDeletePost(props.row.id)"
          />
        </q-td>
      </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue";
import postsService from "src/services/posts";
import { useQuasar } from "quasar";
import { useRouter } from "vue-router";

export default defineComponent({
  name: "IndexPage",
  setup() {
    const posts = ref([]);
    const { list, remove } = postsService();

    const columns = [
      { name: "id", field: "id", label: "Id", sorteble: true, align: "left" },
      {
        name: "title",
        field: "title",
        label: "Titulo",
        sorteble: true,
        align: "left",
      },
      {
        name: "author",
        field: "author",
        label: "Autor",
        sorteble: true,
        align: "left",
      },
      { name: "actions", field: "actions", label: "Ações", align: "right" },
    ];

    const $q = useQuasar();
    const router = useRouter();

    onMounted(() => {
      getPosts();
    });

    const getPosts = async () => {
      try {
        const data = await list();
        posts.value = data;
      } catch (error) {
        console.log(error);
      }
    };

    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
          title: "Deletar",
          message: "Deseja deletar este post ?",
          cancel: true,
          persistent: true,
        }).onOk(async () => {
          await remove(id);
          $q.notify({
            message: "Apagado com sucesso",
            icon: "check",
            color: "positive",
          });
          await getPosts();
        });
      } catch (error) {
        $q.notify({
          message: "Error ao apagar post",
          icon: "check",
          color: "negative",
        });
      }
    };

    const handleEditarPost = (id) => {
      router.push({ name: "formPost", params: { id } });
    };

    return {
      posts,
      columns,
      handleDeletePost,
      handleEditarPost,
    };
  },
});
</script>
